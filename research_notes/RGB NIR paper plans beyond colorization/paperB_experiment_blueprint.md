# Paper B experiment blueprint: "Does NIR Make Low-Light Enhancement Useful for Machines? A Protocol Study and a No-Regret, Illuminant-Aware NIR-Guided Enhancer"

Researched 2026-11-03. Sources: arXiv HTML full texts, GitHub READMEs, and files read from shallow `git clone`s of the official repositories. No code was run.

**How to read this file**
- **Cited Findings:** facts read today from primary sources, linked inline.
- **(wave1):** taken from the earlier verified notes in this folder.
- **(mem):** from memory, not re-verified today.
- **(est.):** my compute or time estimate, not a published number.
- **Scope:** this file does not repeat the thesis plan in `/home/user/lowlight/research/PUBLICATION_ROADMAP.md`. It reuses that plan's protocol names P0–P4, its "regret rate" idea, the SCI bug fix and the Zero-DCE++ crop warning.

---

## Q1. Enhancer inventory: code, weights, training data, inputs, cost, and whether each can be retrained on Real-NAID in about one GPU-day

### Takeaway
- **Ready to run today:** every RGB-only arm has official code and weights. Two NIR-guided arms ship weights:
  - **DarkVisionNet:** the checkpoint is inside the GitHub repo, trained on DVD.
  - **NAID/SFM:** NIR-NAFNet, NIR-Restormer, NIR-Uformer and NIR-Xformer weights are on Baidu Netdisk, trained on Real-NAID.
- **No public code:** FCENet's announced repo was not reachable on 2026-11-03, and Kim & Baek (CVPR 2025) have no code link. Both must be reimplemented. Both are small.
- **Retraining:** NIR-NAFNet and DVN are realistic to retrain on Real-NAID within about one 24 GB GPU-day (est.). NIR-Restormer needs about 1.5–2 days, or a smaller batch.
- **NVEU** works on 240×320 video, uses unpaired 850 nm active-NIR data and needs an old stack (torch 1.8 + detectron2). Treat it as optional and qualitative only.

### Cited Findings

**NIR-guided arms**

**DarkVisionNet (DVN, AAAI 2022)**
- **Code and weights:** official repo [megvii-research/DVN](https://github.com/megvii-research/DVN). A shallow clone contains `checkpoints/Fusion/models/model_epoch_80.pth` (≈84 MB) and `checkpoints/Recons/models/model_epoch_{2,4}.pth`, so pretrained weights ship in the repo.
- **DVD dataset:** Google-Drive link in the [README](https://github.com/megvii-research/DVN), laid out as `DVD_train_raw`, `DVD_test` and `DVD_real` folders, each with RGB/ and NIR/.
- **Training is two-stage** (`training.yml` in the repo): first an autoencoder (`Recons`, 4 epochs), then fusion (80 epochs). Patch size 128, batch size 16, LR 2e-4 → 1e-6. The config lists 2 GPUs.
- **Input pipeline** (`dataset.py` in the repo):
  - Input is a raw `.npy` mosaic from an RGB-IR sensor (the `OV4686BayerPatternV2_RGB` pattern).
  - The loader darkens it (`brightness_aug`, base brightness 10, range 2–20) and adds calibrated noise at a random σ ∈ [1, 16) (`noise_k.pkl`).
  - It then converts the noisy raw to sRGB (`show_bgr`, `show_ir`).
  - So the network itself takes **sRGB-like RGB plus NIR**. Retraining on Real-NAID's PNGs needs a new dataloader.
- **Precedent on IDD-AW:** DVN pretrained on DVD was already used to preprocess IDD-AW. The ICPR 2024 3rd-place team (SemSeggers) ran DVN, then fine-tuned Mask2Former, and got **67.58 mIoU**. They report that DVN "introduced discolouration and artefacts" which did not hurt training ([ICPR 2024 report, 2409.05327](https://arxiv.org/abs/2409.05327)).

**NAID / SFM (IEEE TMM 2024/25)**
- **Code:** [ronjonxu/NAID](https://github.com/ronjonxu/NAID). Models in the repo are `nirnaf`, `nirrestormer` and `niruformer` (`models/*.py`).
- **Downloads:** Real-NAID and the pretrained checkpoints are on **Baidu Netdisk only** (dataset password `ik9u`, weights `jcdb`).
- **Training recipe** (`train.sh`, and the [paper](https://arxiv.org/html/2404.08514v4)):
  - `--patch_size 128 --niter 240 --batch_size 32 --lr 3e-4 --lr_decay_iters 40`.
  - The dataloader defines an epoch as 16,000 random patches.
  - The paper trains all models for **120k iterations at batch 32 with 128×128 patches**, LR 3e-4 halved every 20k iterations for real data, on an **RTX A6000**.
- **Data format:** the Real-NAID loader reads 8-bit images (`/255.`), i.e. **sRGB RGB + NIR + GT**. The test split has 30 scenes × 3 noise levels.
- **Real-NAID capture** ([paper](https://arxiv.org/html/2404.08514v4)):
  - Huawei X2381-VG surveillance camera with a **built-in 750–1100 nm NIR illuminator** (active NIR).
  - Noisy images at ISO 4000 / 12000 / 32000; clean reference at ISO 600.
  - Cropped from 2048×3840 to 2048×2160; 100 scenes, randomly split 70/30.
- **Inference cost at 1792×1008** (paper Table II; A6000 timings):

  | Model | Time (ms) | Params (M) |
  |---|---|---|
  | NIR-Restormer | 2747 | 30.26 |
  | NIR-NAFNet | 462 | 35.06 |
  | NIR-Uformer | 2500 | 24.51 |
  | NIR-Xformer | 7884 | 30.92 |
  | DVN | 761 | 11.73 |

  SFM adds about 1 ms to NIR-NAFNet.

**FCENet (CVPR 2025)**
- **Code:** the [paper](https://arxiv.org/html/2412.16645v2) says "code will be released at github.com/yuchenwang815/FCENet". On 2026-11-03 `raw.githubusercontent` returned 404 and `git clone` asked for credentials, i.e. **the repo is not public**.
- **Architecture:** a two-stage U-Net. Stage 1 pre-denoises the RGB. Stage 2 fuses NIR and RGB with FDSM (frequency dynamic selection) and FEFM (frequency exhaustive fusion). Loss is Charbonnier plus a frequency loss (weight 0.1).
- **Training:** Adam, batch 16, 80 epochs on DVD (Poisson-Gaussian noise 1–16 after darkening, as in DVN), cosine LR. On IVRG (EPFL) it uses 16,374 patches, 100 epochs and σ = 25/50/75.
- **Cost:** 4.44 M params and 19.52 GFLOPs. The lite FCENet-L has 1.16 M params and 5.71 GFLOPs.
- **Not tested on Real-NAID.**

**Kim & Baek (CVPR 2025)**
- **Venue confirmed:** CVPR 2025, pp. 11482–11492 ([CVF](https://openaccess.thecvf.com/content/CVPR2025/html/Kim_Pixel-aligned_RGB-NIR_Stereo_Imaging_and_Dataset_for_Robot_Vision_CVPR_2025_paper.html)).
- **Fusion method** ([arXiv 2411.18025](https://arxiv.org/html/2411.18025v2)):
  1. Convert RGB to HSV.
  2. Form the fused brightness V as a weighted sum of RGB-V and NIR, with **learned spatially varying α, β**.
  3. Encoders are ResNet-based with 256 channels; NIR is copied ×3. Features are combined with attentional feature fusion, then a conv decoder predicts α and β.
  4. Apply a guided filter using NIR as guidance.
- **Training:** photometric plus stereo losses only, with **downstream models frozen**.
- **No GitHub or code URL** appears in the arXiv HTML (only funding-agency links).
- **Data:** 43 real scenes and 80k frames from an **active NIR** robot rig.

**NVEU (ICCV 2023)**
- **Code:** [MyNiuuu/NVEU](https://github.com/MyNiuuu/NVEU). Checkpoint and the FMSVD dataset are on Google Drive.
- **Environment:** Python 3.8, **torch 1.8.0 + cudatoolkit 11.1, detectron2**, plus a custom `package_core` build.
- **FMSVD** (wave1): 240×320 frames, unpaired, 850 nm LEDs at night.

**RGB-only arms**

- **HVI-CIDNet (CVPR 2025; NTIRE 2025 LLIE winner)** ([repo](https://github.com/Fediory/HVI-CIDNet), [paper](https://arxiv.org/html/2502.20272)):
  - Weights for each training set (LOLv1, LOLv2-real, LOLv2-syn, SICE, SID, …) are on Hugging Face, Baidu and OneDrive. The README states every weight was trained on its training set only.
  - The README warns that some table rows use "GT mean" correction at test time.
  - 1.88 M params, 7.57 GFLOPs (measurement size not stated in the text I read).
  - Width and height must be divisible by 8; pad otherwise.
  - The 2025-01-31 code update adds random-gamma training "to improve cross-dataset generalization".
- **Retinexformer** ([repo](https://github.com/caiyuanhao1998/Retinexformer)):
  - Weights for LOL-v1/v2, SID, SMID, SDSD, FiveK and NTIRE 2024, plus ready-made **ExDark enhancement results**.
  - The README advises against the GT-mean test setting.
- **SCI** ([repo](https://github.com/vis-opt-group/SCI)):
  - The README lists 500/100 MIT pairs and 500/50 LSRW pairs as training data.
  - **Save the enhanced output `r`, not the illumination `i`.** This is the confirmed bug in the thesis roadmap.
- **Zero-DCE++** ([repo](https://github.com/Li-Chongyi/Zero-DCE_extension)): ships `Epoch99.pth`. Training data is SICE Part 1 (mem). The roadmap warns that the test script crops to multiples of 12, so **pad instead**.
- **KinD++** ([repo](https://github.com/zhangyhuaee/KinD_plus)): TensorFlow; checkpoints on Google Drive or Baidu. Trained on a rearranged LOL plus 260 synthetic pairs (README).
- **PairLIE (CVPR 2023)** ([repo](https://github.com/zhenqifu/PairLIE)): weights in `./weights`; training/test data on Google Drive.
- **SNR-Aware (CVPR 2022)** ([repo](https://github.com/dvlab-research/SNR-Aware-Low-Light-Enhance)): trained on LOL-v1/v2, SID and SMID.
- **LIME:** no official Python release from the authors. Use a common reimplementation (mem).

### Inferences

**Enhancer table for the paper** ("1-day retrain on Real-NAID" assumes one 24 GB GPU such as an RTX 3090/4090/A5000; all estimates):

| Arm | Code / weights | Trained on | Input | NIR band | ≈ time at 1024×768 (scaled from published numbers) | 1-day retrain on Real-NAID? |
|---|---|---|---|---|---|---|
| LIME | reimpl. | none (optimisation) | sRGB | – | CPU, about 1 s | n/a |
| Zero-DCE++ | ✓ / ✓ | SICE (mem) | sRGB | – | <10 ms | n/a (zero-reference; can finetune on IDD-AW LL unpaired in hours) |
| SCI (save r) | ✓ / ✓ | MIT / LSRW | sRGB | – | <10 ms | n/a |
| KinD++ | ✓ (TF1) / ✓ | LOL + synthetic | sRGB | – | ~0.3–1 s (est.) | n/a |
| PairLIE | ✓ / ✓ | LOL / SICE pairs | sRGB | – | ~20 ms (est.) | n/a |
| SNR-Aware | ✓ / ✓ | LOL / SID / SMID | sRGB | – | ~0.1 s (est.) | n/a |
| Retinexformer | ✓ / ✓ | LOL etc. | sRGB | – | ~0.1–0.2 s (est.) | n/a |
| HVI-CIDNet | ✓ / ✓ | LOL, SICE, SID… | sRGB (pad to /8) | – | ~50 ms (est.) | n/a |
| DVN | ✓ / ✓ (in repo) | DVD (RGB-IR sensor, synthetic noise) | sRGB + NIR | DVD sensor (not stated) | ≈ 761 ms × (0.79/1.81 MP) ≈ **0.33 s** | **Yes** (est. 12–20 h, after writing a PNG dataloader) |
| NIR-NAFNet (SFM) | ✓ / ✓ (Baidu) | Real-NAID (active NIR) | sRGB + NIR | 750–1100 nm | ≈ **0.2 s** | **Yes** (120k it × bs32 × 128²; est. 10–16 h) |
| NIR-Restormer (SFM) | ✓ / ✓ (Baidu) | Real-NAID | sRGB + NIR | 750–1100 nm | ≈ **1.2 s** (tile to avoid OOM) | Borderline: use bs16, or accept ~1.5–2 days (est.) |
| FCENet / FCENet-L | **reimplement** | DVD / IVRG | sRGB + NIR | — | small (4.4 M params) | Yes once reimplemented. Coding time is the bottleneck: ~1–2 weeks (est.) |
| Kim & Baek fusion | **reimplement** | own active-NIR robot data | sRGB + NIR → 3-channel fused image | 750–1000 nm | small | Yes (~1 day), but there is no paired GT, so train either on Real-NAID fidelity or with a frozen-task loss (state which) |
| NVEU (optional) | ✓ / ✓ | FMSVD (850 nm active, unpaired) | video, 240×320 | 850 nm LED | low-res only | No; inference-only arm |

**Recommended arm list**
1. **RGB-only, thesis set:** LIME, Zero-DCE++, SCI-r, KinD++, PairLIE, SNR-Aware, Retinexformer.
2. **RGB-only, modern:** HVI-CIDNet. Optionally a NAFNet/Restormer RGB-only denoiser trained on Real-NAID RGB. This is the **"same backbone without NIR"** control that SFM's own table uses: Restormer-Baseline vs NIR-Restormer.
3. **NIR-guided, off-the-shelf:** DVN (DVD weights) and NIR-NAFNet / NIR-Restormer (Real-NAID weights).
4. **NIR-guided, retrained on a common recipe:** DVN, NIR-NAFNet, FCENet-L (reimplemented) and Kim & Baek (reimplemented). Each is trained on (a) Real-NAID (active) and (b) synthetic passive-NIR pairs built from IDD-AW (see Q6). This separates **method** from **training data**.
5. **Fusion references:** 4-channel early stacking (SegFormer with a 4-channel patch-embed) and one feature-fusion RGB-X segmenter. Candidates are CMNeXt / StitchFusion style; repo URLs not verified today (mem).
6. **Proposed method.**

**Engineering checklist**
- Run every enhancer at **native 1024×768**, padded rather than cropped.
- Cache outputs as 8-bit PNG. Quantization is a control in the roadmap.
- Log per-image runtime.
- For Restormer-class models use 512² tiles with 32 px overlap.
- Keep KinD++ (TF1) and NVEU (torch 1.8) in separate conda environments.

### Gaps
- FCENet code may appear later. Recheck the repo before reimplementing, and consider emailing the authors.
- I measured no runtimes. All 1024×768 timings are linear pixel-count scalings of the NAID A6000 numbers, so treat them as order-of-magnitude.
- FLOP figures conflict across papers. NAID lists NIR-Restormer at 89.17 G (128² patch); FCENet's table lists it at 45.31 G. Report your own fvcore/thop numbers at 1024×768.
- Which NIR band DVD's sensor covers was not stated in the material read.
- Real-NAID and the NAID weights are Baidu-only, which can be slow or blocked outside China. Plan for a mirror, or ask the authors.

---

## Q2. Downstream models, training on IDD-AW at 1024×768 on one GPU, splits, polygon→box conversion, and other RGB-NIR detection data

### Takeaway
- **Main stack:**
  - Segmentation: SegFormer-B2 (mmsegmentation) as the workhorse; Mask2Former as a second architecture family; one InternImage-B run to anchor to the WACV numbers.
  - Detection: YOLO11m (Ultralytics) plus RT-DETR-L or Faster R-CNN.
- **Boxes:** IDD-AW uses AutoNUE-style `*_gtFine_polygons.json`. AutoNUE's `createLabels.py --instance True` writes instance PNGs, and mmdetection's Cityscapes→COCO converter can be adapted to turn them into COCO boxes. No ready-made IDD-AW detection script exists.
- **Second detection set:** TRNT (AAAI 2026) is public (Baidu, CC BY 4.0, with AUNet code and checkpoint). It is the only public RGB-NIR pedestrian detection set with night frames that I found.

### Cited Findings

**IDD-AW splits and labels**
- Splits per the [ICPR 2024 report](https://arxiv.org/abs/2409.05327) (train / val / test):

  | | Rain | Fog | Low-light | Snow | Total |
  |---|---|---|---|---|---|
  | Train | 1062 | 1033 | 684 | 651 | 3430 |
  | Val | 120 | 154 | 100 | 101 | 475 |
  | Test | 318 | 313 | 216 | 248 | 1095 |

  For the competition the test GT was held back and conditions were merged (wave1 audit).
- The [IDDAW_kit](https://github.com/Furqan7007/IDDAW_kit) ships a **different split**:
  - `train.txt` has 3926 images from 162 drive sequences; `val.txt` has 1072 images from 41 sequences.
  - **No sequence appears in both** (checked from the cloned lists).
  - `label.txt` lists `idd_aw/test/level3Ids/.../*_labellevel3Ids.png` for all 1072, and `val_s.txt` / `label_s.txt` hold 247 images (probably the snow test subset).
- Mask generation: follow [AutoNUE public-code](https://github.com/AutoNUE/public-code), using `createLabels_iddaw.py` in place of `createLabels.py` (kit README). **That file is not in the kit repo** (cloned 2026-11-03).
- Evaluation uses **26 level-3 classes**, with mean and std given in `info.json`. The kit's `safe_iou.py` implements the semantic hierarchy (e.g. road↔drivable fallback, person↔rider as N2 neighbours).
- AutoNUE details:
  - `createLabels.py` supports `--id-type {id, csId, csTrainId, level3Id, level2Id, level1Id}`, `--instance True` and `--panoptic True`.
  - **Instance segmentation masks** use `--id-type id` and write `_gtFine_instanceids.png`.
  - In `helpers/anue_labels.py`, `hasInstances=True` for person, animal (ignoreInEval), rider, motorcycle, bicycle, autorickshaw, car, truck, bus, caravan/trailer (ignoreInEval) and vehicle fallback.
  - Each label also carries a `csTrainId`, i.e. a **Cityscapes-19 mapping**.
- mmdetection's [`tools/dataset_converters/cityscapes.py`](https://github.com/open-mmlab/mmdetection/blob/main/tools/dataset_converters/cityscapes.py) converts `*_gtFine_instanceIds.png` into COCO JSON with `pycocotools`, using `cityscapesscripts` labels.
- IDD-AW camera: JAI FS-3200D-10GE 2-CMOS prism; VIS 400–670 nm, NIR 740–1000 nm. Frames were paired by timestamp and checked by hand ([IDD-AW paper](https://arxiv.org/abs/2311.14459)). The paper does not describe its training schedule (no iterations or crop size in the HTML text).

**Training recipes reported on IDD-AW**
- **ICPR winner (KweenCoders):** FusionNet RGB-NIR preprocessing, then SegFormer. Trained on **IDD for 150k iterations, then IDD-AW for 160k, on 2×V100**. Loss is CE + Dice. Code at [maturiaiswarya/SegFormer](https://github.com/maturiaiswarya/SegFormer) ([ICPR report](https://arxiv.org/abs/2409.05327)).
- **ICPR 2nd (SixthSense):** Mask2Former Swin-L (216 M params). Cityscapes, then IDD (60k iterations, about 2 days on a DGX A100), then the last 4 layers on IDD-AW (90k steps, about 2 days). Uses per-weather normalisation. Same report.

**Model zoos**
- **mmseg SegFormer on Cityscapes, 1024×1024 crop, 160k iterations, 8×1 batch** ([zoo](https://github.com/open-mmlab/mmsegmentation/tree/main/configs/segformer)):
  - MiT-B2: **7.42 GB/GPU**, 81.08 mIoU.
  - MiT-B4: **15.07 GB/GPU**, 81.89 mIoU.
  - Checkpoints downloadable.
- **mmseg Mask2Former on Cityscapes, 512×1024, 90k iterations** ([zoo](https://github.com/open-mmlab/mmsegmentation/tree/main/configs/mask2former)):
  - R-50: 5.67 GB, 80.44.
  - Swin-T: 6.36 GB, 81.71.
  - Swin-B (in22k): 10.89 GB, 83.52.
- **InternImage UperNet-B on Cityscapes, 512×1024, 160k:** 83.18 / 83.97 mIoU, 128 M params, 2369 GFLOPs. Config and Hugging Face checkpoint in the [InternImage segmentation README](https://github.com/OpenGVLab/InternImage/tree/master/segmentation).
- **Ultralytics YOLO11, COCO at 640** ([docs](https://docs.ultralytics.com/models/yolo11/)):

  | Model | mAP | T4 TensorRT (ms) | Params (M) | FLOPs (B) |
  |---|---|---|---|---|
  | YOLO11n | 39.5 | 1.5 | 2.6 | 6.5 |
  | YOLO11s | 47.0 | 2.5 | 9.4 | 21.6 |
  | YOLO11m | 51.5 | 4.7 | – | – |

- **RT-DETR in Ultralytics** ([docs](https://docs.ultralytics.com/models/rtdetr/)): RT-DETR-L 53.0 AP at 114 FPS on a T4; RT-DETR-X 54.8 AP at 74 FPS. Trained through the same `model.train(...)` API.

**Other RGB-NIR detection data**
- **TRNT** ([paper](https://arxiv.org/html/2602.06363), [repo](https://github.com/medicineqian/AuNet)):
  - Content: 8,281 pixel-aligned RGB-NIR-TIR triplets; 7,026 train / 1,255 test (split follows CVC-14); 640×480; every image has ≥1 pedestrian.
  - Capture: **08:00–22:00**, from ground PTZ and UAV (7,429 / 852).
  - Release: dataset on Baidu (code `drnt`), **CC BY 4.0**; AUNet checkpoint on Baidu (code `best`). Labels are TXT files.
  - The paper says NIR "captures abundant person texture… particularly under **active nighttime illumination**".
- **NIRPed** ([repo](https://github.com/XiaobiaoDai/NIRPed)): a night pedestrian set, **NIR-only**, with COCO-format JSON, 150 GB on ScienceDB and CC BY 4.0. It has no RGB pair, so it cannot be used for RGB-NIR enhancement.
- **Scoop check:** an arXiv API query for abstracts containing "RGB-NIR" + "low-light" (2023–2026) returned only DVN, ICPR 2024, TRNT, FD-INR (2604.16800) and Niu et al. MM'26 (2607.29684). A query for "IDD-AW" returned only the dataset and ICPR papers ([arXiv API](https://export.arxiv.org/api/query?search_query=abs:%22RGB-NIR%22+AND+abs:%22low-light%22)). **No paper found that evaluates NIR-guided enhancers by downstream tasks on IDD-AW.**

### Inferences

**Split decision**
- **Primary: the WACV/kit split** (3926 / 1072, sequence-disjoint).
  - Its per-image label paths are listed, and the WACV baselines (RGB 62 / NIR 57 / stack 63 on low-light) were reported "for IDD-AW test set". That is most likely this split (inference).
  - If the download really contains the 1072 test label PNGs, report on it. Low-light share expected ≈ 216 images.
- **Secondary: the ICPR split's 100 low-light val images.** Use these to compare with ICPR numbers.
- **Fallback if test labels are missing:** a sequence-disjoint re-split of the labelled low-light pool. Fix it once and publish the file lists.
- **Hold out a 10% sequence-disjoint "dev" split from train.** All early stopping, gate thresholds and hyper-parameter choices use dev, never test.

**Segmentation recipe for one 24 GB GPU** (all times est.)

| Model | Crop / batch | Schedule | Time per run (3090/4090-class) | Use |
|---|---|---|---|---|
| SegFormer-B2 | 768×768 crops from 1024×768 images (resize ratio 0.5–2.0), batch 2, AMP; Cityscapes-pretrained init (mmseg checkpoint) | AdamW 6e-5, poly, **40k iterations** (about 23 epochs over 3.4k images) | ~5–7 h | Workhorse for every P1/P3/P4 cell × 3 seeds |
| SegFormer-B4 | batch 1 + grad-accum 2 | 40k iterations | ~10–14 h | Top-3 arms, 1 seed |
| Mask2Former R-50 or Swin-T | 512×1024-class crop, batch 2 | 40k iterations | ~8–12 h | Second architecture family, top-6 arms |
| InternImage-B UperNet | needs a DCNv3 CUDA build | 40k iterations, batch 1–2 | ~14–20 h | One run each for RGB / NIR / stack / best NIR-guided / proposed, to anchor to WACV's 62 / 57 / 63 |

**Variants**
- **P0 (zero-shot daylight model):**
  - Use mmseg's Cityscapes SegFormer-B2 (81.08) directly. Evaluate on the 19 Cityscapes classes via `csTrainId`.
  - Optionally use a SegFormer-B2 trained on IDD Part I (same 26-class level-3 space; ~10–12 h, est.).
  - WACV's InternImage-b P0 reference on low-light: Cityscapes-trained 42, ACDC-trained 42, IDD-trained 50 (Q3).
- **4-channel stacking:** replace the first patch-embed conv with 4 input channels. Initialise the NIR filter as the mean of the RGB filters. Normalise NIR with its own mean/std, computed from train.

**Detection recipe**
- **Classes (6):** person, rider, two-wheeler (motorcycle + bicycle), autorickshaw, car, large vehicle (truck + bus).
  - Ignore regions: vehicle fallback, animal, caravan, trailer (iscrowd=1).
  - Riders are labelled separately from their bikes in IDD, so keep rider as its own class. Merge into "person" only for COCO zero-shot (P0).
- **Conversion steps:**
  1. `python preperation/createLabels.py --datadir $IDDAW --id-type id --instance True` (AutoNUE helpers on PYTHONPATH; use the IDD-AW variant of the script if the authors send it).
  2. Copy mmdet's `cityscapes.py` converter. Swap `cityscapesscripts.helpers.labels` for `anue_labels`. Map label ids to the 6 classes. Write `instances_{train,val}.json`.
  3. Convert COCO → YOLO txt with Ultralytics' COCO converter (`ultralytics.data.converter.convert_coco`, mem) or a 30-line script.
  4. Drop boxes under 8 px high at 1024×768. Visually check 50 images per condition.
  5. Prefer **instance-mask-derived (modal) boxes** over raw polygon extents. Cityscapes-style polygons are drawn back-to-front and can extend behind occluders (inference).
- **Training budgets** (est.):
  - **YOLO11m:** `imgsz=1024, rect=False, batch=8–12, epochs=100, close_mosaic=10`, COCO-pretrained. About 4–6 h per run on ~3.4–3.9k images.
  - **RT-DETR-L:** `imgsz=1024, batch=4–6, epochs=72`. About 8–12 h.
  - **Faster R-CNN R50-FPN (mmdet):** 1024×768, batch 4, 2× schedule (24 epochs). About 6–8 h.
  - Run all three detectors for P1/P2 (3 seeds). Run P3/P4 for the top-6 arms only.
- **P0 detection:** COCO-pretrained YOLO11m, RT-DETR-L and Faster R-CNN, evaluated on person (+rider → person), car, truck, bus, motorcycle and bicycle. Autorickshaw has no COCO class, so treat it as an ignore region.

**TRNT usage** (secondary detection benchmark)
- **Night subset:** the paper gives no per-image time-of-day list, so define night by mean luminance (e.g. Y < 40/255) and verify by hand.
- **Runs:** YOLO11m trained on TRNT RGB (P1), then P2/P3 with each enhancer (RGB + NIR → enhanced RGB).
- **References:** the 4-channel stack, and the published AUNet RGB-NIR result.
- **Cost:** 640×480, so each run takes ~1–2 h (est.).
- **Caveat:** TRNT's night NIR is probably actively illuminated (paper wording; not verified). That makes it the "active-NIR" side of the passive-vs-active contrast, with IDD-AW as the passive side.

### Gaps
- Check whether the IDD-AW download contains (a) `*_polygons.json`, (b) the 1072 test `level3Ids`, and (c) `createLabels_iddaw.py`. The kit README says it should, but the script is missing from the repo.
- Native IDD-AW resolution and whether the PNGs are gamma-encoded were not found in the paper. Both matter for the NIR/RGB ratio features in Q6.
- I found no published IDD-AW detection or instance results, and no public IDD-trained SegFormer checkpoint (not verified).
- I did not check whether TRNT's TXT boxes are YOLO-normalised or pixel units.

---

## Q3. Numbers to beat, with sources

### Takeaway
- **Restoration:**
  - Real-NAID: NIR-Restormer **26.22 / 25.51 / 24.76 dB** (LPIPS 0.265 / 0.293 / 0.315).
  - DVD: FCENet **32.43 / 30.37 / 28.78 dB** at σ = 2 / 4 / 6.
- **Segmentation:** IDD-AW InternImage-b low-light: RGB **62**, NIR **57**, stack **63**. Best overall result is ICPR's **68.3–68.5 mIoU** (different split).
- **Detection:**
  - TRNT AP50: RGB **77.0**, RGB-NIR AUNet **91.63**.
  - Kim & Baek, frozen YOLO: **0.756 → 0.809** mAP.
  - No IDD-AW detection numbers exist; this paper would set the first.

### Cited Findings

**Real-NAID** (PSNR / SSIM / LPIPS at low / mid / high noise; [NAID Table III and the SFM ablation table](https://arxiv.org/html/2404.08514v4))

| Method | Low | Mid | High |
|---|---|---|---|
| **NIR-Restormer** | **26.22 / .7963 / .265** | **25.51 / .7767 / .293** | **24.76 / .7626 / .315** |
| NIR-Xformer | 26.19 / .7959 / .264 | 25.38 / .7729 / .293 | 24.66 / .7595 / .319 |
| NIR-NAFNet | 26.06 / .7905 / .274 | 25.26 / .7676 / .303 | 24.48 / .7503 / .321 |
| NIR-Uformer | 25.91 / .7919 / .276 | 25.14 / .7714 / .299 | 24.28 / .7534 / .321 |
| DVN | 25.96 / .7853 / .298 | 24.93 / .7578 / .332 | 23.95 / .7360 / .382 |
| MNNet | 25.68 | 24.64 | 23.36 |
| SANet | 24.93 | 23.74 | 22.69 |
| CUNet | 24.05 | 23.29 | 22.41 |
| FGDNet | 24.25 | 22.89 | 21.86 |
| Restormer, RGB-only | 25.89 / .7842 / .294 | 24.98 / .7572 / .333 | 23.82 / .7297 / .387 |
| Xformer, RGB-only | 25.91 | 24.81 | 23.73 |
| NAFNet, RGB-only | 25.71 | 24.76 | 23.71 |
| Uformer, RGB-only | 25.56 | 24.52 | 23.31 |
| Restormer-Baseline (NIR fused by plain addition, no SFM) | 26.12 / .7948 / .265 | 25.38 / .7733 / .293 | 24.61 / .7587 / .316 |

- **SFM adds only about 0.1–0.15 dB** over adding NIR naively (NIR-Restormer vs Restormer-Baseline).
- **NIR adds about +0.3 / +0.5 / +0.9 dB** over RGB-only Restormer at low / mid / high noise.

**DVD** (PSNR / SSIM at σ = 2 / 4 / 6; FCENet Table 1, [arXiv 2412.16645](https://arxiv.org/html/2412.16645v2))

| Method | σ = 2 | σ = 4 | σ = 6 | FLOPs (G) / Params (M) |
|---|---|---|---|---|
| **FCENet** | **32.43 / .963** | **30.37 / .950** | **28.78 / .939** | 19.52 / 4.44 |
| FCENet-L | 31.87 | 29.91 | 28.36 | 5.71 / 1.16 |
| NIR-Restormer | 31.81 / .957 | 29.81 / .942 | 28.31 / .928 | 45.31 / 30.26 |
| DVN | 31.50 / .955 | 29.62 / .940 | 28.26 / .927 | 48.30 / 6.97 |
| MPRNet, RGB-only | 31.78 | 29.37 | 27.84 | – |
| Restormer, RGB-only | 31.30 | 29.20 | 27.76 | – |

- **IVRG/EPFL** (σ = 25 / 50 / 75, same paper): FCENet 33.26 / 29.08 / 25.48; NIR-Restormer 32.86 / 28.77 / 25.30.
- NAID's own DVD table gives DVN 29.69 / 0.9062 / LPIPS 0.236 at its lower noise setting. That **does not match** FCENet's DVD protocol ([NAID](https://arxiv.org/html/2404.08514v4)). Never mix DVD numbers across papers.

**IDD-AW** (InternImage-b, mIoU %, Rain / Fog / LL / Snow / All; [WACV Table 2](https://arxiv.org/abs/2311.14459))

| Train set | Rain | Fog | LL | Snow | All |
|---|---|---|---|---|---|
| Cityscapes (RGB) | 46 | 45 | **42** | 43 | 46 |
| ACDC (RGB) | 47 | 51 | **42** | 38 | 48 |
| IDD (RGB) | 52 | 55 | **50** | 33 | 54 |
| IDD-AW RGB | 62 | 64 | **62** | 53 | 64 |
| IDD-AW NIR | 61 | 58 | **57** | 51 | 61 |
| IDD-AW RGB+NIR (stack) | 66 | 65 | **63** | 53 | 67 |

- **ICPR 2024 test** (mIoU / SmIoU / SmIoU-tp; [report](https://arxiv.org/abs/2409.05327)):

  | Entry | Input | mIoU / SmIoU / SmIoU-tp |
  |---|---|---|
  | KweenCoders (FusionNet + SegFormer) | RGB+NIR | 68.32 / 64.73 / 55.07 |
  | SixthSense (Mask2Former Swin-L) | RGB only | 68.54 / 63.52 / 52.08 |
  | SemSeggers (DVN + Mask2Former) | RGB+NIR | 67.58 / 62.15 / 51.60 |
  | Organiser baseline | RGB+NIR | 67.30 / 62.26 / 51.50 |
  | Organiser baseline | RGB | 64.70 / 60.56 / 51.32 |

- WACV-split SmIoU (all): mIoU 64, SmIoU-tp 60, SmIoU 51. LL row: 62 / 58 / 48 ([WACV Table 3](https://arxiv.org/abs/2311.14459)).

**Detection**
- **TRNT** (AP50 / AP75 / mAP with CSPDarknet53; [TRNT Table 2](https://arxiv.org/html/2602.06363)):

  | Input | AP50 | AP75 | mAP |
  |---|---|---|---|
  | RGB | **77.00** | 22.03 | 32.00 |
  | NIR | 72.61 | 20.21 | 29.83 |
  | RGB-NIR (AUNet) | **91.63** | 49.64 | 50.29 |
  | RGB-NIR-TIR (AUNet) | 97.07 | 67.78 | 60.08 |

  With ResNet50: RGB 78.03, RGB-NIR 91.48. These are **all-day test numbers, not night-only**.
- **Kim & Baek** (frozen YOLO mAP on their data; [arXiv](https://arxiv.org/html/2411.18025v2), Table 1b):

  | Input | mAP |
  |---|---|
  | RGB | 0.756 |
  | NIR | 0.703 |
  | YCrCb fusion | 0.571 |
  | Bayesian fusion | 0.745 |
  | DarkVision | 0.762 |
  | Adaptive | 0.773 |
  | VGG-NIR | 0.726 |
  | HSV baseline | 0.744 |
  | **Theirs** | **0.809** |

### Inferences

**Target numbers for the proposed method** (go/no-go thresholds; my proposals)
- **Real-NAID:** within **−0.2 dB** of NIR-Restormer at every noise level (≥ 26.0 / 25.3 / 24.6), with ≥ 3× lower latency.
  - It is not designed as a fidelity champion; fidelity is a sanity check.
  - Stretch goal: beat NIR-Restormer at high noise (≥ 24.9).
- **DVD (FCENet protocol):** ≥ 30.0 dB at σ = 4. FCENet-L reaches 29.91 with 1.16 M params, so this is plausible for a light R-branch.
- **IDD-AW low-light, P2** (raw-trained SegFormer-B2, frozen):
  - ≥ +2 mIoU over raw.
  - ≥ +1 over the best NIR-guided baseline.
  - **No condition** (rain / fog / snow) below raw by more than 0.5.
- **IDD-AW low-light, P3:** reach or exceed the 4-channel stacking upper reference (WACV gap stack − RGB on LL is +1 with InternImage-b). Aim to close ≥ 50% of the gap to a feature-fusion segmenter.
- **Detection:** ≥ +1.5 AP50 on IDD-AW-LL boxes over raw under P2. On TRNT-night, ≥ +3 AP50 over raw (RGB 77.0 all-day). AUNet's 91.6 is a dedicated multimodal detector, so treat it as an upper reference, not a target.
- **ICPR comparison:** use the 100 LL val images, but mainly as context. That split's test GT is not public.

### Gaps
- No published night-only TRNT numbers.
- No published IDD-AW low-light numbers for SegFormer, Mask2Former, DVN-preprocessing or FusionNet per condition. The ICPR report gives overall numbers only (per-condition tables not found).
- Kim & Baek's detection labels, classes and IoU threshold are not described in the text read.

---

## Q4. Annotating light-source type on IDD-AW low-light cheaply, and checking agreement

### Takeaway
- **Physics:** Elvidge et al. 2010 gives the basis:
  - High-pressure sodium and metal-halide lamps have their strongest or a strong line at **819 nm** (NIR-bright).
  - Incandescent and halogen lamps peak at **900–1,050 nm** (NIR-very-bright).
  - White **LEDs have "virtually no emission beyond 800 nm"** (NIR-dark).
  - **Fluorescent lamps are used extensively for outdoor lighting in India**, the country where IDD-AW was captured.
- **Light-source taxonomy:** sodium (HPS/LPS); incandescent/halogen; white LED; fluorescent/CFL; metal halide; vehicle headlights (mixed); active IR (expected none); dusk skylight; mixed/unknown.
- **Recommended protocol:** label image-level dominant and secondary sources on all ~1,000 low-light images with 2 annotators, and region-level boxes on a 200-image subset. Report Cohen's κ / Krippendorff's α, adjudicate disagreements, and calibrate an automatic NIR/RGB-ratio labeller against the human labels.

### Cited Findings
- Elvidge et al. 2010, *Sensors*, "Spectral Identification of Lighting Type and Character" ([PMC3274255](https://pmc.ncbi.nlm.nih.gov/articles/PMC3274255)):
  - Spectra of 43 lamps, 9 types: liquid fuel, pressurized fuel, incandescent (incl. quartz halogen), mercury vapor, fluorescent, LPS, HPS, metal halide, LED.
  - **HPS:** "The strongest emission line is from the set of sodium emissions at **819 nm**"; golden-orange light.
  - **LPS:** a single strong line at 589 nm plus a much smaller line at 819 nm, with an IR-reflecting outer envelope.
  - **Metal halide:** "Each … had a strong emission at **819 nm**"; in 2 of 4 lamps it was the strongest line.
  - **Incandescent:** blackbody-shaped spectra with peak radiance at **900–1,050 nm**. Quartz halogen peaks at 970–980 nm and is spectrally indistinguishable from standard incandescent.
  - **LED:** white LEDs are a blue chip (450–460 nm) plus phosphor; "The measured LED lamps had **virtually no emission beyond 800 nm**."
  - "Fluorescent lights … are also used extensively for **outdoor lighting in countries such as India**."
  - Lighting type is best discriminated with **blue, green, red + a broad NIR band** (Landsat-TM-like). That is the same 4-band geometry as RGB + NIR in IDD-AW.
- Fredembach & Süsstrunk 2009 show that NIR/RGB brightness ratios reveal the illuminant ([EPFL record](https://infoscience.epfl.ch/record/133341); wave1; the page returned HTTP 429 today, so not re-read).
- IDD-AW's NIR channel covers **740–1000 nm** ([paper](https://arxiv.org/abs/2311.14459)), so it includes the 819 nm sodium line and most of the incandescent peak.
- **Agreement statistics:** Cohen's κ / Fleiss' κ, with the Landis & Koch (1977) benchmarks (0.61–0.80 "substantial") ([PubMed 843571](https://pubmed.ncbi.nlm.nih.gov/843571/); standard reference, page not re-fetched).

### Inferences

**Expected NIR/RGB signature per class** (my physics inference from Elvidge's spectra and the 740–1000 nm band; to be measured on IDD-AW, not assumed)

| Class | Visual cues in RGB | Expected ρ = NIR / luminance in lit regions |
|---|---|---|
| Sodium (HPS/LPS) | Orange, near-monochrome; low colour rendering (colours collapse to orange-grey) | High |
| Incandescent/halogen (old headlights, shop bulbs) | Warm yellow-white; halo | Highest |
| Metal halide | Cool/neutral white | High |
| White LED (modern streetlights, newer headlights) | Cool bluish-white; sharp beam edges | **Very low**; NIR image dark where RGB is lit |
| Fluorescent/CFL (shops, tube lights) | Greenish-white | Low (unverified; mercury-argon lamps may show a few NIR lines) |
| Vehicle headlights | Must be labelled by appearance: halogen (warm), HID (bluish with a sharp cut-off), LED | – |
| Active IR | Not expected in IDD-AW. Signature would be uniform NIR flood, radial falloff, RGB dark | – |
| Dusk/sky | Ambient skylight; non-zero sky luminance | Moderate |

**Annotation protocol** (≈ 3–4 person-days in total)
1. **Automatic pre-labels** (1 day of coding):
   - For each image, find light-emitting blobs (saturated RGB pixels, or the top 0.5% luminance) and lit-surface regions (top 10% luminance, excluding blobs).
   - Compute blob chromaticity (r, g) and hue, blob CCT estimate, ρ_blob, ρ_lit, global mean luminance and global ρ.
   - Cluster the results and show them to annotators as hints. **Hide them on the test split, so human labels are not anchored.**
2. **Image-level labels** for all ~1,000 low-light images: dominant source, plus secondary sources (multi-label), plus a confidence of 1–3.
   - Use a fast tool (Label Studio choice task) showing RGB, NIR and a ρ heat-map side by side.
   - About 10–15 s per image → ~3–4 h per annotator per 1,000 images.
3. **Region-level labels** on 200 images, stratified by the image-level labels and split 100 train / 100 test: boxes on each light source and its lit footprint.
   - About 2–3 min per image → ~8–10 h per annotator.
   - These supervise and evaluate the light-source head of the reliability estimator.
4. **Agreement:**
   - Two independent annotators on all images. Report Cohen's κ (image-level dominant class), Krippendorff's α (multi-label), and box-level agreement (IoU ≥ 0.5, matched-class accuracy).
   - Target κ ≥ 0.6. If κ < 0.6, merge confusable classes (e.g. metal halide → "NIR-rich white"; fluorescent + LED → "NIR-poor white"), recompute, and report both.
   - A third person adjudicates disagreements.
5. **Physical validation:** report the distribution of ρ per human-labelled class (violin plot) and a simple classifier using only (ρ, r, g, CCT) with its accuracy against human labels.
   - This is the falsifiable link to Elvidge's spectra: sodium and halogen ρ should exceed LED ρ.
   - If the separation fails (AUC < 0.75), the "illuminant-aware" claim weakens. The paper must then present the gate as a learned reliability gate validated against NIR SNR only.
6. **Ratio thresholds:** do not hard-code thresholds from the literature. Neither source gives camera-specific thresholds for the JAI FS-3200D. Fit them on the 100 region-labelled training images, freeze them, and report them.
7. **Linearisation:** compute ρ in a linearised domain. If the PNGs are gamma-encoded, apply the inverse sRGB curve to both channels. Exclude saturated pixels.

### Gaps
- IDD-AW's actual lamp mix (sodium vs LED vs fluorescent share in 2021–23 Indian cities) is unknown and must be measured. If LED dominates, the "NIR-rich" strata may be small.
- I have no measured JAI FS-3200D NIR response curve. JAI publishes one (not verified).
- The NIR signature of fluorescent lamps was not verified in the text I read.

---

## Q5. Statistics: seeds, paired bootstrap, Holm, regret rate and stratified analyses

### Takeaway
- **Seeds:** 3 seeds for every trained downstream model and for the proposed enhancer.
- **CIs:** hierarchical paired bootstrap (resample seeds, then images; 10,000 replicates). Dataset-level metrics are recomputed from summed confusion matrices or COCOeval on each resample.
- **Multiple comparisons:** Holm correction within each comparison family.
- **Regret:** define it per image with a noise floor estimated from raw-vs-raw seed disagreement.
- **Stratify by:** condition, light source, darkness quintile, object size and class group.

### Cited Findings
- **Seed controls:** the thesis roadmap already mandates 3 seeds, paired-bootstrap CIs with Holm, identical tuning budgets, both augmentation orders, and identity / quantization / brightness-matched controls (`/home/user/lowlight/research/PUBLICATION_ROADMAP.md` §1.1).
- **Regret null:** the roadmap's Paper 2 uses raw-vs-raw seed disagreement as a null distribution for per-object utility (same file, §1.2). The same idea is reused below for the regret noise floor.
- **Cost of SFM:** NAID shows that SFM's gain over naive addition is ~0.1–0.15 dB ([Real-NAID table](https://arxiv.org/html/2404.08514v4)). Small differences like that need CIs, not single numbers.
- **Theory to cite:** Turgeman & Tirer (ICLR 2026) show that preprocessing can help with finite training data ([arXiv 2512.21315](https://arxiv.org/abs/2512.21315); wave1). This motivates reporting the P1 − P3 gap together with training-set size.
- **References:** Holm (1979), *Scandinavian Journal of Statistics* 6:65–70 (standard; [JSTOR 4615733](https://www.jstor.org/stable/4615733), not re-fetched). Bouthillier et al. 2021 on benchmark variance ([arXiv 2103.03098](https://arxiv.org/abs/2103.03098); mem).

### Inferences

**Definitions to put in the paper**
- **Metrics per image i:**
  - Segmentation: present-class mIoU on image i, m_i(·), computed over classes present in the GT of i. Report it **only for regret**. Headline mIoU is always dataset-level.
  - Detection: **per-object outcomes** at a fixed operating point (score ≥ 0.25, IoU ≥ 0.5), plus per-image F1.
- **Image-level difference:** Δ_i = m_i(D(y_i)) − m_i(D(x_i)), where x is raw, y is enhanced and D is the same downstream model.
- **Noise floor τ:** the 95th percentile of |m_i(D_s1(x_i)) − m_i(D_s2(x_i))| over seed pairs, on the dev split. This is seed disagreement on raw input.
- **Regret rate** RR = (1/N) Σ_i 1[Δ_i < −τ]. **Win rate** WR = (1/N) Σ_i 1[Δ_i > τ]. Always report both.
- **Expected regret** ER = mean_i max(0, −Δ_i).
- **Tail risk:** CVaR₁₀ = mean of the worst 10% of Δ_i.
- **Object-level regret (detection):**
  - "Lost objects" = GT objects detected on raw but missed on enhanced.
  - "Gained objects" = the reverse.
  - Report the net and the lost/gained ratio, stratified by size.
- **Relative regret:** regret vs the RGB-only path. Replace x by E_rgb(x) in Δ. This measures the NIR branch's own contribution.

**Inference procedure**
- **Bootstrap:** for each replicate b = 1..10,000:
  1. Draw 3 seeds with replacement.
  2. Draw N test images with replacement (block bootstrap by drive sequence if sequences repeat in test).
  3. Recompute the dataset-level mIoU (from summed confusion matrices) or AP (COCOeval on the subset) for both arms on the **same** resample.
  4. Store the difference.
- **Report:** the mean Δ and the 2.5 / 97.5 percentiles. One-sided p = fraction of replicates with Δ ≤ 0.
- **Families for Holm:**
  - (F1) each arm vs raw within one protocol and dataset (~14 tests);
  - (F2) the proposed method vs each baseline (~10 tests);
  - (F3) ablation rows vs the full model (~15 tests).
  - Holm-adjust within each family; never pool across tables.
  - Also report Wilcoxon signed-rank on Δ_i as a robustness check.
- **Minimum detectable effect:** before Week 3, bootstrap raw-vs-raw (different seeds) on the ~216 low-light test images. That gives the CI half-width, expected around 0.5–1.0 mIoU (est.). The 2-mIoU go/no-go must be clearly above it.
- **Interaction tests** (the scientific core): fit a per-image linear mixed model Δ_i ~ arm × stratum + (1 | sequence), or test via bootstrap of differences-in-differences. Key effects:
  - (i) NIR-guided − RGB-only gain × light-source class (NIR-rich vs NIR-poor);
  - (ii) the same × darkness quintile;
  - (iii) × condition.

**Strata**
- **Condition:** rain / fog / LL / snow.
- **Light source:** human image-level dominant class (Q4); region-level for the 100 region-labelled test images.
- **Darkness:** per-image mean luminance Y = 0.2126R + 0.7152G + 0.0722B on the raw sRGB, binned into quintiles within LL. Also bin on mean NIR, because NIR-dark images are a separate failure mode.
- **Object size:** box height terciles at 1024×768, plus COCO small / medium / large (<32², 32²–96², >96²).
- **Class group:** traffic participants vs stuff. Report SmIoU-tp for safety.
- **Protocol:** P0–P4 and both augmentation orders for the top arms.

### Gaps
- Holm and Landis & Koch are standard references; their pages were not re-fetched today.
- Whether IDD-AW test sequences contain near-duplicate frames is unknown (a 3-second frame filter was used per the paper), so the need for block bootstrap is unknown.

---

## Q6. Protocol matrix and the proposed-method recipe (what exactly to run)

### Takeaway
- Run a **protocol × arm × task grid** on IDD-AW (seg + det) and TRNT (det): P0, P1, P2, P3 and P4, times 14 arms.
- Train the proposed **y = E_rgb(x) + g ⊙ R(x, n)** in three stages:
  1. fidelity pre-training on Real-NAID plus synthetic passive-NIR pairs made from labelled IDD-AW day images;
  2. task-driven training through **frozen** raw-trained downstream models, with a regret hinge;
  3. no further stages; the gate is initialised closed.
- Test transfer to **unseen** downstream models, to rule out adversarial overfitting to the training model.

### Cited Findings
- **Training recipes to reuse:**
  - SFM / NAID: 120k iterations, batch 32, 128² patches, LR 3e-4 halving every 20k ([NAID](https://arxiv.org/html/2404.08514v4)).
  - FCENet: Charbonnier + 0.1 × frequency loss, 80 epochs ([FCENet](https://arxiv.org/html/2412.16645v2)).
  - DVN's darken-plus-noise synthesis: `brightness_aug` plus calibrated noise σ ∈ [1, 16) (DVN `dataset.py`, [repo](https://github.com/megvii-research/DVN)).
- **Frozen-downstream training is established:** Kim & Baek train the fusion module without training downstream models ([arXiv](https://arxiv.org/html/2411.18025v2)).
- **Task-driven fusion with a learnable task loss exists for IR-visible fusion:** TDFusion ([arXiv 2412.03240](https://arxiv.org/abs/2412.03240)). Restoration can lower detector accuracy and can be constrained by a detection loss: Sun et al., NeurIPS 2022 ([poster](https://neurips.cc/virtual/2022/poster/54700); wave1). Both must be cited and differentiated.
- **Novelty risk:** the roadmap rated a standalone "no-regret enhancement" paper 3/10 because TaskGuard, Sun 2022 and DBDiff exist (`PUBLICATION_ROADMAP.md` §0). Paper B's novelty must therefore rest on **NIR + illuminant physics + a passive-NIR study**, not on the hinge itself.

### Inferences

**Protocol matrix (main paper)**

| Protocol | Downstream training | Test input | Tasks / data | Seeds |
|---|---|---|---|---|
| P0 | Daylight model, frozen (Cityscapes SegFormer-B2; COCO YOLO11m / RT-DETR / Faster R-CNN) | each arm's output | IDD-AW all conditions; TRNT | 1 (public checkpoint) |
| P1 | Raw RGB, IDD-AW train | raw | seg + det | 3 |
| P2 | P1 models, frozen | each arm's output | seg + det | 3 (inherited) |
| P3 | Retrained on each arm's output | same arm | seg (all 14 arms), det (top 6) | 3 |
| P4 | Mixed raw + arm 50/50 | raw and arm | top 6 arms | 3 |
| Upper references | 4-channel stack; feature-fusion RGB-X | RGB + NIR | seg (+ det stack) | 3 |

**Application scope:** apply enhancers to **all** IDD-AW conditions (rain / fog / snow) as well as LL. A "universal preprocessor" is the realistic deployment, and NIR effects in weather are a secondary research question. Report LL as the headline.

**Proposed method**
- **E_rgb:** a frozen pretrained RGB-only enhancer. Default is HVI-CIDNet (fast, modern). Swap to Retinexformer or Zero-DCE++ in an ablation.
- **Optional raw fallback:** E_rgb'(x) = x + a(x) ⊙ (E_rgb(x) − x), with a scalar or low-resolution gate a. Then a = 0 returns raw x and **"no regret vs raw" becomes reachable by construction**, not only "no regret vs the RGB-only path".
- **R(x, n):** NIR-guided residual branch. Default is a NAFNet-width-32 U-Net with SFM-style fusion. An FCENet-L-style variant is the swap. Output is an RGB residual, bounded by tanh.
- **Gate g = σ(h([x, n, F_illum, σ̂_N, σ̂_RGB, Ȳ]))**, at 1/4 resolution, then bilinear upsampling and a guided filter on NIR:
  - F_illum: per-region ρ = N / Y (linearised), blob chromaticity and CCT, and the light-source head's softmax p(class), trained on the 100 region-labelled train images plus weak labels from the ratio classifier.
  - σ̂_N, σ̂_RGB: per-patch noise estimates (MAD of fine wavelet coefficients).
  - Ȳ: local mean luminance.
  - **Initialise the gate bias at −3**, so g ≈ 0.05 and training starts on the RGB-only path.
- **Training data:**
  - (a) **Real-NAID train** (70 scenes × 3 ISO; active NIR) → fidelity.
  - (b) **Synthetic passive-NIR night pairs from IDD-AW day-weather training images** (rain / fog / snow ≈ 2.7k images, with segmentation labels and boxes):
    1. Linearise RGB.
    2. Darken by k ~ LogUniform(1/200, 1/10).
    3. Multiply by an illuminant colour drawn from {sodium, halogen, LED, fluorescent}.
    4. Darken NIR by k·ρ_class, with ρ_class taken from the measured per-class ratios (Q4).
    5. Add Poisson-Gaussian noise (DVN-style calibrated, or ISO-matched from Real-NAID noisy-clean statistics).
    6. Re-apply gamma.
  - The clean target is the original RGB, and **labels come for free**.
  - Limitation to state: per-material NIR/RGB reflectance is kept from daylight. That is approximate for line-spectrum lamps.
  - (c) **Real IDD-AW LL train** (684 or the kit equivalent) for the task and self-supervised losses only (no clean GT).
- **Stage A** (fidelity, ~1 GPU-day est.): Charbonnier + 0.1 × frequency loss on (a) + (b). g is free, with an L1 sparsity penalty λ_g = 0.01.
- **Stage B** (task, ~1–1.5 GPU-days est.): on (b) + (c), minibatches of 1024×768 crops at 768², batch 4.

  L = L_task(D(y)) + λ_exp · L_exp + λ_n · L_noise + λ_r · Σ_i max(0, L_task(D(y_i)) − min[L_task(D(x_i)), L_task(D(E_rgb(x_i)))]) + λ_f · L_fid(on b) + λ_g · ‖g‖₁

  - D is the frozen P1 SegFormer-B2 (seed 0) for segmentation, optionally plus YOLO11m (seed 0) via its loss for a joint model.
  - L_exp is the Zero-DCE exposure-control loss (target 0.5–0.6).
  - L_noise is a Neighbor2Neighbor-style self-supervised denoising loss, or TV on flat regions.
  - Start from λ_r = 1, λ_exp = 1, λ_n = 0.1, λ_f = 1.
- **Cross-model test (mandatory):** evaluate y with D seeds 1 and 2, Mask2Former, InternImage-B and the detectors. None of these were used in training. The gain must transfer; otherwise reviewers will call it an adversarial perturbation of D.
- **P3 for the proposed method:** retrain SegFormer on y. This is the only setting where the downstream model adapts; no-regret is not guaranteed there and must be reported empirically.

**Compute budget** (est., one 24 GB GPU)

| Block | GPU-h |
|---|---|
| Caching enhancer outputs (IDD-AW 5k + TRNT 8k, all arms) | 15–25 |
| Real-NAID retraining (DVN, NIR-NAFNet, NIR-Restormer @ bs16, FCENet-L, Kim & Baek, RGB-only NAFNet control) | 120–150 |
| Synthetic-pair retraining (same 5 NIR arms) | 80–100 |
| Segmentation P1/P3 (14 arms × 3 seeds × ~6 h) | ≈ 250 |
| Segmentation P4 (6 × 3 × 6 h) | ≈ 110 |
| Mask2Former and InternImage checks | ≈ 120 |
| Detection YOLO11m P1/P3/P4 (7 × 3 × 5 h) | ≈ 105 |
| RT-DETR and Faster R-CNN (P1 + top 3, 3 seeds) | ≈ 150 |
| TRNT | ≈ 40 |
| Proposed method (stages A + B, 3 seeds) | ≈ 100 |
| Ablations (15 rows × ~1 day of stage B + P2 eval, 1 seed; top-5 rows × 3 seeds) | ≈ 450 |
| **Total** | **≈ 1,540–1,600 GPU-h**, about 64–67 GPU-days |

- The **6-month window offers about 4,300 GPU-h**, but the roadmap's Paper 2 needs about 550 GPU-h over Dec–Feb. Schedule Paper B's heavy P3/P4 grid in Jan–Mar, and cut seeds or arms first if contention arises.
- **Cheapest cuts:** P4 for 3 arms only; ablations at 1 seed except the 5 key rows; Mask2Former for the top 3 only.

### Gaps
- The JAI camera's noise model for IDD-AW is unknown. Synthetic noise is therefore borrowed from DVN or Real-NAID; state this as a limitation.
- Whether the frozen-D task loss overfits to D cannot be known in advance. The cross-model test is the safeguard.

---

## Q7. Full ablation table (named rows)

### Takeaway
About 20 named rows grouped into gate, illuminant, losses, downstream coupling, backbones, data and controls. The most important for reviewers:
- **NIR-shuffled control:** NIR from a different image.
- **Capacity-matched control:** a luminance copy instead of NIR.
- **Oracle per-image gate:** the upper bound.
- **Cross-model transfer:** gains measured on downstream models not used in training.

### Cited Findings
- SFM's own ablation pattern (baseline vs +SFM; block counts) is in [NAID Tables IV, X and XI](https://arxiv.org/html/2404.08514v4). FCENet ablates FDSM and FEFM branches against a two-stage U-Net with additive fusion ([FCENet Tables 3–5](https://arxiv.org/html/2412.16645v2)). Both papers ablate fusion modules only, never downstream utility. That is the gap these rows fill.
- The roadmap requires identity-I/O, quantization and brightness-matched controls (`PUBLICATION_ROADMAP.md` §1.1).

### Inferences

**Setup:** all rows use P2 with frozen SegFormer-B2 seed 1 (not the training seed). Report IDD-AW LL mIoU, ΔmIoU vs raw, regret rate, rain/fog/snow mean, and Real-NAID high-noise PSNR. Rows marked ★ get 3 seeds.

| ID | Row name | What changes |
|---|---|---|
| A0 ★ | **Full model** | — |
| A1 ★ | **No gate (g ≡ 1)** | Always inject the NIR residual |
| A2 ★ | **RGB-only path (g ≡ 0)** | = E_rgb(x); the no-regret floor |
| A3 | **Constant gate g ≡ 0.5** / **random gate** | Control: is the gate's spatial structure doing the work? |
| A4 ★ | **Gate without illuminant features** | Gate sees only (x, n); a generic learned reliability gate |
| A5 | **Illuminant ratio stats only** | No light-source head |
| A6 | **Light-source head only** | No ρ statistics |
| A7 | **No NIR-noise estimate** | Drop σ̂_N |
| A8 | **Image-level vs region-level vs pixel-level gate** | Granularity |
| A9 ★ | **No regret hinge** | λ_r = 0 |
| A10 | **Hinge vs raw only** / **hinge vs RGB-only only** | Choice of reference |
| A11 ★ | **Fidelity loss only** | Stage A only, no task loss |
| A12 | **Task loss only** | No exposure/noise self-supervised losses, no fidelity |
| A13 | **Frozen vs fine-tuned downstream** | Joint training of enhancer + SegFormer; compare with P3 |
| A14 | **Downstream used in training** | SegFormer vs YOLO vs both, tested cross-task |
| A15 | **E_rgb backbone swap** | CIDNet / Retinexformer / Zero-DCE++ |
| A16 | **R backbone swap** | NAFNet-SFM / FCENet-L-style / Kim & Baek HSV-V fusion |
| A17 | **NIR noise injection during training** | On / off (Poisson-Gaussian on the NIR input) |
| A18 ★ | **Active vs passive NIR training data** | Real-NAID only / synthetic passive only / both |
| A19 | **Illuminant mix in synthesis** | Uniform over classes vs IDD-AW-measured prior vs a "no illuminant model" single ρ |
| A20 | **NIR-shuffled control** | NIR from a random other image of the same condition |
| A21 | **Capacity-matched no-NIR control** | Feed Y(x) or a gray copy instead of n |
| A22 | **Oracle gate** | Per image, pick the best of g ∈ {0, 1} using GT; an upper bound |
| A23 | **Augmentation order** | Enhance-first vs augment-first, for P3 of the top arms |
| A24 | **Quantization / I/O control** | Identity network saved through the same PNG pipeline |
| A25 | **Cross-model transfer** | Gains on unseen D (seed 2, Mask2Former, InternImage, YOLO) |

**Expected readings**
- A20 and A21 should erase most of the gain. If they don't, the gain is capacity or regularisation, not NIR information.
- A4 vs A0 measures what the illuminant physics adds. **It is the headline ablation.**
- A18 tests the "active-NIR training overestimates NIR utility" claim.

### Gaps
- Ablations cost about 1 GPU-day each with stage B retraining. If the budget is cut, run A6, A8, A12, A16 and A19 on the 100-image LL val subset only, and say so.

---

## Q8. Figures and tables expected in a strong Q1 paper of this type

### Takeaway
Plan **8–9 figures and 8–10 tables**. In priority order:
- the pipeline diagram;
- the protocol × arm heat-map with significance marks;
- Δ-vs-darkness curves;
- per-light-source bars plus a ρ-validation violin plot;
- regret CDFs;
- gate maps against light-source boxes;
- qualitative grids;
- a restoration-vs-utility scatter;
- an efficiency table.

### Cited Findings
- The comparable papers report their contributions as follows. In each case the evaluation unit is a table plus qualitative grids:
  - **IDD-AW** reports per-condition mIoU tables, cross-condition matrices, class-wise mIoU vs SmIoU and qualitative grids per condition ([WACV](https://arxiv.org/abs/2311.14459)).
  - **NAID** reports per-noise-level PSNR / SSIM / LPIPS with FLOPs, time and params in one table, and SFM ablations ([TMM](https://arxiv.org/html/2404.08514v4)).
  - **FCENet** opens with a PSNR-vs-FLOPs bubble plot (Fig. 1) and includes visualisations of its frequency filter kernels ([CVPR'25](https://arxiv.org/html/2412.16645v2)).
  - **Kim & Baek** report frozen-detector mAP for many fusion methods in one table, with qualitative detection overlays ([CVPR'25](https://arxiv.org/html/2411.18025v2)).
  - **TRNT** reports a modality-combination table across backbones and a missing-modality robustness table ([AAAI'26](https://arxiv.org/html/2602.06363)).
- Task-driven IR-visible fusion papers at this level evaluate fused images with downstream detectors or segmenters (e.g. TDFusion, [arXiv 2412.03240](https://arxiv.org/abs/2412.03240)). I did not verify their exact figure inventory today.

### Inferences

**Figures**

| # | Content |
|---|---|
| F1 | **Teaser:** one LL scene under sodium light and one under LED. Rows: raw / best RGB-only / DVN / SFM / ours, with segmentation overlays and ΔmIoU. Inset: the NIR/RGB ratio map. |
| F2 | **Pipeline:** E_rgb, R(x, n) and the gate with its illuminant estimator (ρ statistics, noise, light-source head). Show the training losses and the frozen D, with the regret-hinge branch drawn explicitly. |
| F3 | **Protocol × arm heat-map** (rows = 14 arms grouped RGB-only / NIR-guided / fusion / ours; columns = P0–P4 × {seg LL, det LL, TRNT-night}). Cells = Δ vs raw, with ● where Holm-adjusted p < 0.05. Diverging palette centred at 0. |
| F4 | **Δ vs darkness:** ΔmIoU (seg) and ΔAP50 (det) against mean-luminance quintile, one line per arm family, with bootstrap 95% bands. A second panel uses NIR mean on the x-axis. |
| F5 | **Light source:** (a) violin plot of ρ per human-labelled class, the physics check; (b) grouped bars of ΔmIoU per class for raw-vs-RGB-only, raw-vs-NIR-guided and ours. Expected: NIR-guided gains concentrate on sodium / halogen, and ours avoids losses on LED. |
| F6 | **Regret:** per-image Δ CDFs (or histograms) for 4–5 arms, with the regret rate and CVaR₁₀ annotated. |
| F7 | **Gate maps:** g overlaid on images next to annotated light-source boxes. Scatter of mean g per region against ρ and σ̂_N. A Real-NAID (active NIR) panel should show g ≈ 1. |
| F8 | **Qualitative grid:** rows = rain / fog / LL-sodium / LL-LED / snow; columns = arms; plus segmentation and detection overlays. |
| F9 | **Restoration ≠ utility:** Real-NAID PSNR against IDD-AW LL ΔmIoU (P2) for all NIR arms, with Kendall τ and its CI. Optionally F9b: object-size-stratified ΔAP bars. |

**Tables**

| # | Content |
|---|---|
| T1 | Datasets: IDD-AW split used, LL counts, TRNT, Real-NAID, DVD; active vs passive NIR; labels. |
| T2 | Enhancer inventory: training data, NIR use, params, GFLOPs and ms at 1024×768 on your GPU. |
| T3 | **Main segmentation:** IDD-AW LL mIoU, SmIoU-tp and ΔmIoU [95% CI] under P0–P4, plus rain/fog/snow means. |
| T4 | **Main detection:** IDD-AW boxes (AP50, AP50:95, APs) for YOLO11m / RT-DETR / FRCNN under P0–P2 (P3/P4 for the top arms); TRNT night. |
| T5 | Restoration fidelity: Real-NAID (3 levels) and DVD (σ = 2 / 4 / 6), with the published rows quoted and your re-runs marked. |
| T6 | Ablation (Q7). |
| T7 | Per-light-source and per-darkness stratified results, with interaction-test p-values. |
| T8 | Efficiency: ms, memory, FLOPs, plus end-to-end enhancer + SegFormer latency. |
| T9 | Annotation statistics and agreement (κ / α, class counts). |
| T10 (suppl.) | Every cell × seed, plus the regret and win rates. |

### Gaps
- I did not systematically survey 2024–26 Information Fusion, TCSVT or TIP figure inventories. The list above is a design recommendation grounded in the closest papers read, not a census.

---

## Q9. Week-by-week timeline (Nov 2026 → submission ~May 2027) with go/no-go checkpoints

### Takeaway
- **Front-load** data, conversion and inference-only experiments (P0 / P2) so the decisive question gets answered by **Week 3**: do off-the-shelf NIR-guided enhancers beat RGB-only enhancers under P2 on IDD-AW low-light?
- GNG-1 (Week 3) and GNG-2 (Week 6) choose between three framings. The method is built in Weeks 6–11, with GNG-3 in Week 11. The grid, ablations and writing fill Jan–Apr.
- **Target:** submit to Information Fusion or IEEE TCSVT around 10–24 May 2027, with June as buffer.

### Cited Findings
- **Thesis-plan constraints** (`/home/user/lowlight/research/PUBLICATION_ROADMAP.md` §2): Paper 1 is due in March 2027 and Paper 2 for ACM MM by April 1. Both compete for the same GPU and the student's time.
- **Data access lead times:** IDD-AW needs IDD registration and a **24 h token** ([kit README](https://github.com/Furqan7007/IDDAW_kit)). Real-NAID, NAID weights and TRNT are **Baidu-only** ([NAID](https://github.com/ronjonxu/NAID), [AuNet](https://github.com/medicineqian/AuNet)). DVD is on Google Drive ([DVN](https://github.com/megvii-research/DVN)).

### Inferences

**Phase 1: data and fast experiments (Weeks 1–6)**

- **W1 (Nov 3–8)**
  - Request the IDD-AW token. Download IDD-AW, DVD, Real-NAID + NAID weights, TRNT, and IDD Part I (optional).
  - Generate level-3 masks and instance PNGs; write the COCO/YOLO conversion; visual QA.
  - Freeze the splits, including the dev split.
  - Fix SCI (save r) and the Zero-DCE++ padding.
- **W2 (Nov 9–15)**
  - Cache outputs of all 8 RGB-only and 3 off-the-shelf NIR arms (DVN, NIR-NAFNet, NIR-Restormer) on IDD-AW and TRNT.
  - Start the P1 SegFormer-B2 raw runs (3 seeds) and P1 YOLO11m runs (3 seeds).
  - Write the bootstrap + Holm + regret scripts.
  - Start image-level light-source labelling (annotator 1).
- **W3 (Nov 16–22): GNG-1**
  - Run P0 and P2 for every cached arm (seg + det). Bootstrap the MDE.
  - **GNG-1:** best off-the-shelf NIR-guided arm − best RGB-only arm under P2 on IDD-AW LL ≥ **+2.0 mIoU**, with the 95% CI lower bound > 0.

    | Result | Decision |
    |---|---|
    | **(a) Pass** | Paper framed as "NIR rescues enhancement". Continue as planned. |
    | **(b) Fail, but heterogeneous**: NIR arms win on NIR-rich images and lose on NIR-poor ones (pre-check with ρ-based pseudo-labels) | Strongest motivation for the illuminant-aware gate. Continue, reframed as "NIR helps only when the illuminant supplies NIR photons". |
    | **(c) Fail uniformly**: no subgroup gains | Retrain NIR arms on synthetic passive pairs (W4–5) before giving up. If it still fails at W6, publish as a **negative protocol study** with the stacking / feature-fusion contrast ("pixel-level NIR guidance is the wrong interface; feature fusion is right"). Drop the method or shrink it to a gating baseline. |

- **W4 (Nov 23–29)**
  - Build the synthetic passive-NIR pair generator.
  - Retrain NIR-NAFNet and DVN on Real-NAID (verify you reproduce ≥ 26.0 / 25.2 / 24.4 dB, i.e. within about 0.1 dB of the paper) and on synthetic pairs.
  - Second annotator, image-level.
- **W5 (Nov 30–Dec 6)**
  - Implement FCENet-L and Kim & Baek. Retrain them (Real-NAID / synthetic).
  - Region-level labels on 200 images. Compute κ / α and adjudicate.
- **W6 (Dec 7–13): GNG-2**
  - Light-source stratification of the P2 results.
  - **GNG-2:** (i) ρ separates NIR-rich from NIR-poor classes (AUC ≥ 0.75), and (ii) the interaction is significant (difference-in-differences of NIR-guided gain between NIR-rich and NIR-poor strata ≥ 2 mIoU, Holm p < 0.05).

    | Result | Decision |
    |---|---|
    | Pass | Full "illuminant-aware" claim. |
    | Fail | Keep the gate but drop the illuminant claim. Reframe as a "NIR-SNR-aware" gate, and move the illuminant analysis to a descriptive section. |

**Phase 2: method (Weeks 7–11)**

- **W7–W8 (Dec 14–27; holiday-light)**
  - Proposed method stage A on (Real-NAID + synthetic).
  - Run the P3 SegFormer grid unattended (RGB-only arms).
- **W9 (Dec 28–Jan 3; holiday)**
  - P3 grid continues unattended.
- **W10 (Jan 4–10)**
  - Stage B (task + regret).
  - P2 eval, plus the cross-model transfer test (unseen D seeds, Mask2Former).
- **W11 (Jan 11–17): GNG-3**
  - **GNG-3:** under P2 on LL the proposed method beats (i) the best NIR-guided baseline by ≥ +1.0 mIoU (CI lower bound > 0) and (ii) raw by ≥ +2.0. Its regret rate is ≤ ½ of the best NIR baseline's. No condition is below raw by more than 0.5. The gains transfer to ≥ 2 unseen downstream models.

    | Result | Decision |
    |---|---|
    | Pass on (i) and (ii) only (no transfer) | Report it honestly and add a fine-tuned-D variant. |
    | Fail | Fall back to the study paper with the gate as a "safe baseline". Still Q1-viable. |

**Phase 3: grid, ablations and remaining runs (Weeks 12–19)**

- **W12–W14 (Jan 18–Feb 7)**
  - Finish the P3 grid (NIR arms + ours) and P4 for the top 6 arms.
  - InternImage-B anchor runs.
  - Detection: P3/P4 for the top arms with YOLO11m; RT-DETR and Faster R-CNN for P1/P2.
- **W15–W17 (Feb 8–28)**
  - Ablations A0–A25 (★ rows × 3 seeds).
  - TRNT experiments.
  - Real-NAID / DVD fidelity runs for the proposed method.
  - Efficiency benchmarking.
  - (The Paper 1 deadline falls in March: keep GPU use for Paper B low in W18–W19 if needed.)
- **W18–W19 (Mar 1–14)**
  - Final statistics (bootstrap, Holm, interaction models). Produce all figures.
  - Freeze the numbers: **no new experiments after W19, except reviewer-proofing controls (A20 / A21 / A24) if they are missing.**

**Phase 4: writing (Weeks 20–28)**

- **W20–W23 (Mar 15–Apr 11)**
  - Write. Prepare the code release (enhancer weights, conversion scripts, light-source labels, split lists).
- **W24 (Apr 12–18)**
  - Internal review; check every cited number against the original papers.
- **W25–W26 (Apr 19–May 2)**
  - Revise. Build the supplementary material (all cells and seeds, annotation guide).
- **W27–W28 (May 3–16)**
  - **Submit** to Information Fusion or IEEE TCSVT (alternative: IEEE TIP if the method result is strong).
  - Buffer to the end of June.

**Risk register**

| Risk | Mitigation |
|---|---|
| Baidu access to Real-NAID / TRNT fails | Ask the authors for a Google Drive or OneDrive mirror in W1 |
| IDD-AW test labels absent | Fall back to the sequence-disjoint re-split |
| FCENet reimplementation drifts from the paper | Report FCENet-L as "our reimplementation", and quote the paper's DVD numbers next to our reproduction |
| Frozen-D overfitting | A25 cross-model test |
| GPU contention with Papers 1/2 | P4 and ablations are the first cuts |

### Gaps
- Journal timelines vary. Information Fusion and TCSVT have no deadlines, so the May target is self-imposed.
- Paper 1 and Paper 2 schedule changes in the roadmap could shift this plan.
