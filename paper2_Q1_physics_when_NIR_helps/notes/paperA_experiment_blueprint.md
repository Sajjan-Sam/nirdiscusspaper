# Paper A experiment blueprint: "Learning Where to Trust NIR" (counterfactually supervised, physics-informed RGB–NIR fusion segmentation)

Researched 2026-11-03. I ran no code. Repositories were shallow-cloned and read as text only.

**How sources are marked**
- Every number carries its source.
- **[mem]** means taken from memory and not re-fetched in this session.
- **[UV]** means unverified or estimated.
- **[wave1]** means verified by an earlier agent; see `wave1_*.md` in this folder.
- The **Inferences** sections hold my plan and recommendations. They are not established facts.

---

## Q1. Datasets and preprocessing: IDD-AW, MCubeS, the secondary RGB-T set, and other RGB-NIR sets

### Takeaway
IDD-AW has no turnkey label pipeline.
- The official kit contains only `safe_iou.py`, `info.json` and split lists. `createLabels_iddaw.py`, which its README tells you to use, is **not in the repo**.
- The download ships IDD-style per-object polygon JSONs (`gtSeg/<seq>/<frame>_mask.json`). You rasterise these to 26 level-3 ids yourself, using the AutoNUE `anue_labels.py` mapping.
- `safe_iou.py` has hard-coded paths, a hard-coded important set of 8 traffic-participant classes, and a buggy "main classes" printout.

MCubeS is turnkey through the StitchFusion and CMNeXt loaders: 512×512 training crops, evaluation at 1024, and NIR is a *warped* channel with a validity mask.

For RGB-T, use MFNet. It has the largest leaderboard (Sigma-B 61.3, CMNeXt-B4 59.9) and a day/night split.

VNHD would be the ideal haze set (same JAI camera as IDD-AW) but shows **no release URL**.

### Cited Findings

**IDD-AW: access, structure and labels**
- **Download workflow.** iddaw.github.io → Dataset → IDD site (idd.insaan.iiit.ac.in/dataset/download/) → register → the IDD-AW link gives a 24 h token. — [IDDAW_kit README](https://github.com/Furqan7007/IDDAW_kit)
- **Mask generation per the kit.** "follow the same instructions as… AutoNUE public-code… just replace createLabels_iddaw.py in place of createLabels.py". The cloned repo (HEAD as of 2026-11-03) contains only `README.md`, `requirements.txt`, `safe_iou.py`, `iddaw/{train.txt, val.txt, label.txt, val_s.txt, label_s.txt, info.json}`. **No `createLabels_iddaw.py`.** — [IDDAW_kit](https://github.com/Furqan7007/IDDAW_kit)
- **AutoNUE usage.** `python preperation/createLabels.py --datadir $ANUE --id-type level3Id --num-workers $C`.
  - `level3Id` masks play the role of Cityscapes trainIds.
  - The script globs `gtFine/*/*/*_gt*_polygons.json` and writes `*_label{idtype}s.png`; `--instance True` uses `json2instanceImg`.
  - So it will **not** find IDD-AW's `*_mask.json` files without renaming or a custom script.
  - Source: [AutoNUE public-code README and preperation/createLabels.py](https://github.com/AutoNUE/public-code)
- **Level-3 ids from `helpers/anue_labels.py`.** Examples: road=0, person=4, traffic sign=18, traffic light=19, sky=25.
  - The README prose swaps sign and light (light 18, sign 19).
  - Trust `anue_labels.py`, which matches IDD-AW `info.json` order: index 18 = "traffic sign", 19 = "traffic light".
  - Sources: [AutoNUE helpers/anue_labels.py](https://github.com/AutoNUE/public-code); [IDDAW_kit info.json](https://github.com/Furqan7007/IDDAW_kit)
- **`info.json`.**
  - 26 classes in order: road, drivable fallback, sidewalk, non drivable fallback, person, rider, motorcycle, bicycle, autorickshaw, car, truck, bus, vehicle fallback, curb, wall, fence, guard rail, billboard, traffic sign, traffic light, pole, obs-str-bar-fallback, building, bridge, vegetation, sky.
  - Identity `label2train`, plus a palette.
  - Mean (RGB) = [73.16, 82.91, 72.39]; std = [47.68, 48.49, 47.74] (0–255 scale).
  - Source: [IDDAW_kit/iddaw/info.json](https://github.com/Furqan7007/IDDAW_kit)
- **Download folder layout.** As used by two independent third-party loaders (not stated officially):
  - `IDDAW/{train,val,test}/{FOG,LOWLIGHT,RAIN,SNOW}/{rgb,nir,gtSeg}/<seq>/<frame>_rgb.png | <frame>_nir.png | <frame>_mask.json`
  - The JSON has `imgWidth`, `imgHeight` and `objects[]`, each with `label`, `polygon` and `deleted`.
  - Some labels carry a `_new` or `group` suffix, and some polygons are JSON strings.
  - Sources: [krnRaj03/iddaw-safe-segmentation src/step0_make_compact.py](https://github.com/krnRaj03/iddaw-safe-segmentation); [AdityaVenkat-20030 README tree](https://github.com/AdityaVenkat-20030/semantic-segmentation-adverse-weather)
- **That third-party converter:**
  - It maps raw labels to level-3 as in AutoNUE (parking→1, rail track→3, animal→4, caravan/trailer/train→12, polegroup→20, tunnel→23, fallback background→25). Unlabeled, ego vehicle, rectification border, out of roi and license plate go to 255.
  - It rasterises at full resolution, then resizes the mask to 1024×768 with nearest neighbour, and RGB/NIR with bilinear.
  - It logs RGB/NIR size mismatches and missing partners.
  - Source: [krnRaj03 step0_make_compact.py](https://github.com/krnRaj03/iddaw-safe-segmentation)
- **Instances.** Annotations are per-object polygons (one `objects[]` entry per object). Instance masks can therefore be rasterised the way AutoNUE `json2instanceImg` does for IDD. No explicit instance-id field was seen in the loader code. — [krnRaj03 loader](https://github.com/krnRaj03/iddaw-safe-segmentation); [AutoNUE](https://github.com/AutoNUE/public-code)
- **Camera.** JAI FS-3200D-10GE: 2× Sony IMX252, **2048×1536 per channel**, 3.45 µm pixels, 123 fps, 8/10/12-bit output. VIS 400–670 nm, NIR 740–1000 nm. No per-channel peak wavelengths are published on the page. — [JAI product page](https://www.jai.com/products/fs-3200d-10ge)
  - The IDD-AW paper confirms the camera and bands; it does not state resolution or NIR illumination. — [IDD-AW arXiv 2311.14459 HTML](https://arxiv.org/html/2311.14459)
- **Official splits (ICPR'24).**

  | Split | Rain | Fog | Low-light | Snow | Total |
  |---|---|---|---|---|---|
  | Train | 1062 | 1033 | 684 | 651 | 3430 |
  | Val | 120 | 154 | 100 | 101 | 475 |
  | Test | 318 | 313 | 216 | 248 | 1095 |

  Source: [ICPR'24 report 2409.05327 HTML, Table 1](https://arxiv.org/html/2409.05327v1)
- **The kit's own lists use an older or different layout.**
  - `train.txt` has 3926 lines (`iddaw/train/leftImg8bit/<seq>/<id>_rgb.png`).
  - `val.txt` and `label.txt` have 1072 lines (`idd_aw/test/leftImg8bit/...` and `idd_aw/test/level3Ids/<seq>/<id>_labellevel3Ids.png`).
  - `val_s.txt` has 248 lines, the same count as the snow test set.
  - Source: [IDDAW_kit/iddaw/*.txt](https://github.com/Furqan7007/IDDAW_kit)
- **Split rule.** All frames of a sequence go to one split. Test sequences are 0.18–0.22 of each condition's sequences, with frame-, instance- and pixel-ratio balancing criteria. — [IDD-AW 2311.14459 HTML](https://arxiv.org/html/2311.14459)
- **Resolution used for training.** Neither the WACV paper nor the ICPR report states 1024×768 ("The 1024×768 resolution is not mentioned anywhere"). The only concrete instance I found is the third-party converter resizing to 1024×768, which is exactly half the native 2048×1536. — [ICPR HTML](https://arxiv.org/html/2409.05327v1); [krnRaj03](https://github.com/krnRaj03/iddaw-safe-segmentation)

**Safe mIoU**
- **The paper's definition.**
  - Tree distance = shortest path ÷ 2, and n = number of levels.
  - Default C_imp = "all classes except Far Objects and Sky"; the "tp" variant = Living Things ∪ Vehicles.
  - Source: [IDD-AW 2311.14459 HTML](https://arxiv.org/html/2311.14459)
- **The ICPR report's definition.** Default C_imp = traffic participants ∪ roadside objects; not listed explicitly. — [ICPR HTML](https://arxiv.org/html/2409.05327v1)
- **What `safe_iou.py` actually does:**
  - It uses neighbour sets N1, N2 and N3 per class, where N3 = all remaining classes, with penalty weights **W = [1/3, 2/3, 1]**.
  - `IMP_CLASSES = ['person','rider','motorcycle','bicycle','autorickshaw','car','truck','bus']`.
  - For an important class, all confusions are penalised. For a non-important class, only confusions *into* important classes are penalised.
  - safe_iou_c = (TP_c − penalty)/(TP+FP+FN)_c.
  - It reads the `val.txt`/`label.txt` lists, rebuilds prediction filenames with `'_'.join(s[24:].split('/'))`, and keeps only images where `int(path.split('/')[7]) in range(0,206)`. That is a sequence-id filter on a hard-coded absolute-path depth.
  - Its "MIoU of main classes" line averages `mIoUs[:len(IMP_CLASSES)]`, the **first 8 classes in L3 order (road … bicycle)**, not the 8 important classes.
  - Source: [IDDAW_kit/safe_iou.py](https://github.com/Furqan7007/IDDAW_kit)

**MCubeS**
- **Basics.** RGB, AoLP, DoLP and NIR; 500 sets from 42 scenes at 1224×1024; split 302/96/102; 20 material classes. — [wave1_datasets_audit.md] (MCubeS CVPR'22; [repo](https://github.com/kyotovision-public/multimodal-material-segmentation))
- **Folder layout and loader** (StitchFusion README tree):
  - Folders: `polL_color, polL_aolp_sin, polL_aolp_cos, polL_dolp, NIR_warped, NIR_warped_mask, GT, SSGT4MS, list_folder, SS`.
  - The loader stacks NIR to 3 channels (uint16 is divided by 65535) and loads `NIR_warped_mask`.
  - Training uses `RandomScaleCrop(base_size=512, crop_size=512)`; evaluation uses `FixScaleCrop(1024)`.
  - Source: [StitchFusion README and semseg/datasets/mcubes.py](https://github.com/LiBingyu01/StitchFusion)
- **RGB-N protocol.** Set `MODALS: ['image','nir']` in `configs/mcubes_rgbadn.yaml`. For RGB-A-D-N use `['image','nir','aolp','dolp']`.

  | Config | Backbone | Batch | Epochs | Crop | Optimiser / LR | Schedule | Loss | AMP | Eval size |
  |---|---|---|---|---|---|---|---|---|---|
  | StitchFusion | MiT-B4 | 8 | 400 | 512×512 | AdamW, 6e-5, wd 0.01 | warmup-poly (10 warmup epochs, power 0.9) | OHEM CE | on | 1024×1024 |
  | CMNeXt (DELIVER) | B2 named, mit_b4 weights | 4 | 500 | 512×512 | same family | same family | same family | off | — |

  - The StitchFusion config is set for 4 GPUs. The CMNeXt config uses DDP.
  - Sources: [StitchFusion configs/mcubes_rgbadn.yaml](https://github.com/LiBingyu01/StitchFusion); [DELIVER configs/mcubes_rgbadn.yaml](https://github.com/jamycheung/DELIVER)

**Secondary datasets**
- **RGB-T configs in StitchFusion:**
  - MFNet (`mfnet_rgbt.yaml`): MiT-B4, 640×480, batch 12, 250 epochs, LR 1.2e-4, AMP.
  - FMB (`fmb_rgbt.yaml`): MiT-B3, 800×600, batch 12, 120 epochs, LR 1.2e-4.
  - Dataset links: [MFNet-pytorch](https://github.com/haqishen/MFNet-pytorch), [FMB/SegMiF](https://github.com/JinyuanLiu-CV/SegMiF).
  - Source: [StitchFusion configs](https://github.com/LiBingyu01/StitchFusion)
- **VNHD** (HSVF, IEEE TMM; arXiv 2507.03893):
  - 2,961 pairs (1,519 hazy, 1,442 clear), native 2048×1536 8-bit PNG.
  - **Same JAI FS-3200D-10GE camera**, coaxial.
  - 6-class semantic annotations (sky, ground, buildings, vegetation, water, vehicles); 600 annotated for validation.
  - Long-range haze at 1 km to more than 10 km.
  - **No dataset or code URL and no release statement**; the paper uses a single β.
  - Source: [HSVF 2507.03893 HTML](https://arxiv.org/html/2507.03893v1)
  - A guessed GitHub repo `Yiiclass/HSVF` does not resolve. — git ls-remote check, this session
- **Other RGB-NIR sets with labels.** Freiburg Forest (366 labelled images [mem]) and TAS-NIR (209 images) are small. FANVID (JAI FS-3200T: VIS, NIR1 700–800 nm, NIR2 820–1000 nm; 5,144 sets at 2048×1536; sunny, cloudy, rainy, snowy) has pseudo-label masks only. — [wave1_datasets_audit.md]

### Inferences

**Step-by-step IDD-AW preprocessing (recommended)**
1. **Download.** Register and download to `data/IDDAW/`. Check the tree matches `{train,val,test}/{FOG,LOWLIGHT,RAIN,SNOW}/{rgb,nir,gtSeg}/<seq>/`. Count train = 3430, val = 475, and test = 1095 (test should have no `gtSeg`).
2. **Write `tools/iddaw_make_labels.py`.** Model it on AutoNUE `json2labelImg.py` plus `helpers/anue_labels.py`; do not copy the third-party mapping blindly.
   - Draw polygons **in file order with later objects overwriting**, as AutoNUE does. Skip `deleted`, strip `_new`, and map `*group` names.
   - Write two outputs:
     - (a) `level3Ids` PNG at **native resolution**, used for evaluation;
     - (b) the same resized nearest-neighbour to 1024×768, used for training.
   - Optionally write `instancelevel3Ids` (id×1000 + k) for the panoptic and instance by-products.
3. **Validate the converter.**
   - Spot-check 20 images by palette overlay.
   - Check that per-class pixel fractions are plausible: road and sky dominate, as in the paper's Figure 2.
   - Check that ignore (255) is roughly 5–15% [UV].
4. **Save the inputs.** Store RGB and NIR at 1024×768 as lossless PNG. Avoid JPEG: compression artefacts would contaminate the noise and CNR descriptors used in C2. NIR is 1-channel; replicate it to 3 channels only inside the loader for MiT patch-embeds pretrained on RGB.
5. **Splits.** Train on **train (3430)**, select checkpoints on a **held-out 10% of train sequences**, and report on **val (475)**, the de facto public protocol. Use test (1095, unlabelled) only for an optional server submission if the leaderboard is still live (unverified), and **never** as an unlabelled pool for headline results (see Q4/C3).
6. **Patch `safe_iou.py` into `tools/eval_iddaw.py`.**
   - Remove the path-depth filter and use your own image lists.
   - Accumulate one 26×26 confusion matrix per image, so the bootstrap can be done later.
   - Report:
     - mIoU;
     - SmIoU with the script's tp important set ("SmIoU-tp-set");
     - SmIoU with the paper's default set (all minus far objects and sky; you must define the far-objects members from the IDD level-1/2 hierarchy, and should state them in the appendix);
     - **mean safe-IoU over the 8 tp classes (indices 4–11)**;
     - per-condition values for each.
   - Also print the script-exact "main classes" number, labelled as such. The ICPR SmIoU(tp) may have been produced by that buggy line. That is a hypothesis; it cannot be verified.
7. **Resolution protocol.** Train at 1024×768: random scale 0.5–2.0, crop 768×768 (or 1024×768 when memory allows), horizontal flip. Infer at 1024×768, upsample logits bilinearly to native 2048×1536, then score. State this explicitly in the paper, since prior work did not.

**MCubeS**
- Clone StitchFusion and set `MODALS: ['image','nir']`.
- Keep 512 crops and 1024 evaluation, and report single-scale evaluation (MSF disabled, as in the configs).
- Use `NIR_warped_mask` as an extra physics cue: invalid NIR means g should be 0. This makes MCubeS a natural sanity test that the gate learns "do not trust NIR where it is undefined".

**Secondary datasets**
- **RGB-T:** **MFNet**, using StitchFusion's `mfnet_rgbt.yaml`. Optionally add FMB, which has fog/rain/night diversity.
- **RGB-NIR off-road:** Freiburg Forest is optional. Keep it to the appendix because it is small.
- **Haze with labels:** email the HSVF authors for VNHD now (week 1). It shares IDD-AW's camera, so it would be an ideal zero-shot haze transfer test.

### Gaps
- **Official `createLabels_iddaw.py` is missing** from the kit. Whether the download's JSON polygon order and labels match AutoNUE exactly is unverified until the data is inspected.
- **Native resolution of the IDD-AW PNGs.** Assumed 2048×1536 from the camera; check `imgWidth`/`imgHeight` in the JSONs.
- **Whether the ICPR leaderboard numbers are on test (1095) or val.** The report doesn't say; it calls it a "public leaderboard". Whether the evaluation server is still live is unknown.
- **Whether NIR at night is passive.** Not stated by the paper or JAI.
- **The exact "far objects" class membership** for the paper-default SmIoU important set. Not given in a list.
- **Number of sequences in val (475).** Unknown, and needed for the cluster bootstrap. Derive it from the folder `<seq>` ids after download.

---

## Q2. Baselines with code, and which are trainable on one 24 GB GPU

### Takeaway
The realistic core set is all trainable on one 24 GB GPU at 1024×768 with AMP and MiT-B2/B4:
- SegFormer-B2 (RGB only, and 4-channel early fusion);
- CMX-B2;
- CMNeXt-B2;
- StitchFusion-B2/B4 (main host architecture: shared backbone, cheapest per modality);
- MMSFormer;
- a frozen DINOv2-B + LoRA re-implementation, since FuseAdapt code is "coming soon".

Do **not** retrain Mask2Former Swin-L or InternImage-B as in the original papers (about 4 A100-days for ICPR #2). Cite their numbers instead.

Sigma, DFormerv2 and GeminiFusion have public code but are RGB-T or RGB-D oriented. Include one or two as "general RGB-X" baselines, with time-boxed porting.

### Cited Findings

**Repositories (existence checked with `git ls-remote` this session)**
- StitchFusion: [github.com/LiBingyu01/StitchFusion](https://github.com/LiBingyu01/StitchFusion). PyTorch on the DELIVER/MMSFormer codebase. Train with `python -m tools.train_mm --cfg configs/<x>.yaml`; evaluate with `tools.val_mm`.
  - The README warns that results differ by "a few tenths" on reproduction. Issue #6 lists missing code parts (fixed 2025-08-25).
  - The repo notes that some .pth files fit only `Block_shared`.
  - Source: [README](https://github.com/LiBingyu01/StitchFusion)
- CMNeXt and CMX (DELIVER codebase): [github.com/jamycheung/DELIVER](https://github.com/jamycheung/DELIVER). Contains `semseg/models/backbones/{cmnext.py, cmx.py}` and `modules/ffm.py` (FRM/FFM). The original CMX repo is `huaaaliu/RGBX_Semantic_Segmentation` ([mem]; ls-remote timed out). — cloned this session
- MMSFormer: [github.com/csiplab/MMSFormer](https://github.com/csiplab/MMSFormer); Sigma: [github.com/zifuwan/Sigma](https://github.com/zifuwan/Sigma); DFormer/DFormerv2: [github.com/VCIP-RGBD/DFormer](https://github.com/VCIP-RGBD/DFormer); GeminiFusion: [github.com/JiaDingCN/GeminiFusion](https://github.com/JiaDingCN/GeminiFusion); CAFuser: [github.com/timbroed/CAFuser](https://github.com/timbroed/CAFuser); SegFormer: [NVlabs/SegFormer](https://github.com/NVlabs/SegFormer) and [mmsegmentation](https://github.com/open-mmlab/mmsegmentation); Mask2Former: [facebookresearch/Mask2Former](https://github.com/facebookresearch/Mask2Former); InternImage: [OpenGVLab/InternImage](https://github.com/OpenGVLab/InternImage); DINOv2: [facebookresearch/dinov2](https://github.com/facebookresearch/dinov2). All repos exist. — ls-remote, this session
- **FuseAdapt** (NeurIPS'26, Zhang & Tan, NUS):
  - The project page says "coming soon" for code, and `devinxzhang/FuseAdapt` does not resolve.
  - Method: a frozen VFM with private Q/V LoRA per modality; FFN low-rank residuals routed across modalities; Predictive Modality Modeling (PMM) with an EMA teacher.
  - Evaluated on MCubeS, DELIVER and MUSES.
  - Source: [FuseAdapt project page](https://devinxzhang.github.io/FuseAdapt-Page/); [OpenReview SWGx6oxOoy](https://openreview.net/forum?id=SWGx6oxOoy)
- **FuseAdapt latency.** At 1024×1024, batch 1, FP32, H100, with three-modality MiT-B0:

  | Model | Latency |
  |---|---|
  | FuseAdapt | 21.05 ms |
  | CMNeXt | 19.63 ms |
  | StitchFusion | 26.69 ms |

  Source: [FuseAdapt page](https://devinxzhang.github.io/FuseAdapt-Page/)
- **ICPR'24 top entries:**

  | Rank | Team | Pipeline | Training | Hardware / time |
  |---|---|---|---|---|
  | #1 | KweenCoders | FusionNet (Jung, Zhou, Feng, *IEEE Access* 8:23912–23919, 2020) image fusion, then SegFormer | IDD 150k iters, then IDD-AW 160k iters; 0.5 CE + 0.5 Dice | 2× V100 |
  | #2 | SixthSense | Mask2Former Swin-L (216 M params), RGB only | Cityscapes, then IDD 60k iters, then last 4 layers on IDD-AW 90k steps | about 2 days each stage on a DGX A100 |
  | #3 | SemSeggers | DarkVisionNet smoothing, then Mask2Former | stopped at 65k iters | 2× A6000, 27 h |

  Source: [ICPR report HTML](https://arxiv.org/html/2409.05327v1)
- **IDD-AW paper baseline.** InternImage-B with "stacked RGB+NIR images as input". Decoder, iterations and resolution are not stated. — [IDD-AW HTML](https://arxiv.org/html/2311.14459)
- **SegFormer memory** (mmseg, Cityscapes 1024×1024 crop, batch 1 per GPU, 8 GPUs, 160k iterations):

  | Backbone | Memory (GB) |
  |---|---|
  | MiT-B0 | 3.64 |
  | MiT-B2 | 7.42 |
  | MiT-B4 | 15.07 |
  | MiT-B5 | 18.00 |

  Source: [mmseg SegFormer README](https://github.com/open-mmlab/mmsegmentation/blob/main/configs/segformer/README.md)
- **Parameters and FLOPs at 1024×1024 (StitchFusion Table 8b):**

  | Model | Modalities | Params | GFLOPs |
  |---|---|---|---|
  | SegFormer-B2 | RGB-L | 25.79 M | 59.27 |
  | CMNeXt-B2 | RGB-L | 58.69 M | 62.94 |
  | StitchFusion-B2 | RGB-L | 25.93 M | 59.93 |

  StitchFusion-B4 with two modalities at 512×512 is 127 GFLOPs. — [StitchFusion 2408.01343 HTML](https://arxiv.org/html/2408.01343)
- **StitchFusion architecture.**
  - One shared MiT backbone processes every modality stream: the same `attn`/`mlp` weights.
  - Low-rank `Bi_direct_adapter`s (dim→8→8→dim, zero-initialised) inject each stream into the others after attention and after the MLP in every block (`Block_every_one`).
  - At each stage, a `FeatureCross` FFM (linear over concatenated streams, plus MixFFN, plus channel attention) produces the fused output.
  - Source: [semseg/models/backbones/stitchfusion.py](https://github.com/LiBingyu01/StitchFusion)

### Inferences

**24 GB feasibility table.** My estimates [UV]:
- Scaling: the 7.42 GB / 15.07 GB mmseg figures scaled by pixel count (×0.75 for 1024×768) and by number of streams.
- AMP: assumes roughly 0.55–0.65× memory.
- Time: for an RTX 3090/4090/A5000-class card.

| Baseline (code) | Role | Input | Config on 1×24 GB | Iters | Est. GPU-h |
|---|---|---|---|---|---|
| SegFormer-B2 RGB (mmseg or StitchFusion with `MODALS:['image']`) | RGB-only lower bound | RGB | AMP, bs 4, crop 768×768 (or bs 2 at 1024×768) | 40k | ~6–8 |
| SegFormer-B2 early-fusion 4-ch | "IDD-AW-paper style" stacking, re-implemented on MiT | RGB+N | Patch-embed conv with 4 input channels; NIR weights = mean of RGB weights | 40k | ~6–8 |
| NIR-only SegFormer-B2 | needed for regret analysis and the NIR column | N | same | 40k | ~6–8 |
| Late fusion (average of logits of the RGB and NIR models) | trivial fusion baseline | both | no training | — | 0 |
| Image-level fusion → SegFormer-B2 | ICPR #1 style | fused RGB | FusionNet code not located; use a classical NIR-luminance fusion as a stand-in and label it so | 40k | ~8 |
| CMX-B2 (DELIVER `cmx.py`) | learned FRM spatial and channel gate | RGB+N | AMP, bs 2, 1024×768 | 40k | ~10–12 |
| CMNeXt-B2 (DELIVER) | strong RGB-X baseline | RGB+N | AMP, bs 2, 1024×768 | 40k | ~10–12 |
| **StitchFusion-B2 (host)** | main host plus baseline | RGB+N | AMP, bs 4 at 768² or bs 2 at 1024×768 | 40k | ~8–10 |
| StitchFusion-B4 | larger-backbone check; MCubeS/MFNet protocol | RGB+N | AMP, bs 2, crop 768² plus grad-checkpointing | 40k | ~16–20 |
| MMSFormer-B4 | MCubeS-strong baseline | RGB+N | AMP, bs 2 | 40k | ~16 |
| DINOv2-B/14 frozen + per-modality LoRA (own FuseAdapt-style re-implementation) | VFM host | RGB+N | 1022×770 (73×55 patches), bs 2, SDPA, LoRA r=8–16 on Q/V, SegFormer head | 20–30k | ~10–15 |
| Sigma-S (Mamba) | RGB-T SOTA family | RGB+N | Needs mamba selective-scan CUDA build; bs 2 | 40k | ~12–15 plus setup risk |
| DFormer-B (v1) | RGB-D family; treats X as a 3-channel image | RGB+N | bs 2 | 40k | ~10 |
| GeminiFusion-B2/B3 | pixel-wise fusion family | RGB+N | bs 2 | 40k | ~12 |
| Mask2Former Swin-L (RGB) | ICPR #2 | RGB | **Do not retrain** (about 4 A100-days on ICPR). Cite 68.54 (leaderboard) / 69.51 (val). | — | — |
| InternImage-B stacking | IDD-AW paper | RGB+N | Optional; needs a DCNv3 CUDA op build. **Cite** 67 / 64 instead. | — | — |

**Notes on the baselines**
- **Learning-rate and iteration recipe** for every IDD-AW baseline:
  - AdamW, LR 6e-5, wd 0.01, poly 0.9 with 1.5k-iteration linear warmup, as in the StitchFusion/CMNeXt family.
  - CE with ignore 255, or OHEM CE as in the configs. Keep one loss across all methods.
  - 40k iterations is about 23 epochs at bs 2, or about 47 epochs at bs 4.
  - Keep the recipe **identical across methods**. This is the "controlled comparison" FuseAdapt emphasises.
- **DFormerv2** uses depth as a geometry prior, so it is not semantically natural for NIR. Use DFormer v1 or omit it and explain why.
- **CAFuser** (RA-L'25) is a condition-aware RGB-X baseline (condition token from RGB). It is highly relevant as a "condition-level gate" competitor. Port it if time allows; otherwise emulate it with an image-level condition gate (ablation row G-img).

### Gaps
- No public FusionNet (Jung 2020) code was located.
- No baseline repo states per-GPU memory for 1024×768 RGB+NIR, so all memory and time numbers above are estimates [UV].
- The KweenCoders SegFormer backbone (B2? B5?) is not stated.
- Whether CAFuser's code supports arbitrary X modalities: not checked.

---

## Q3. Numbers to beat, with sources

### Takeaway
- **IDD-AW:** the fusion bar is low. On the leaderboard, the best mIoU is 68.54 (RGB-only Mask2Former Swin-L) and the best SmIoU is 64.73 (FusionNet + SegFormer). On val, the only reported number is 69.51 mIoU (Mask2Former Swin-L RGB). Per-condition numbers exist only as integers from InternImage-B.
- **MCubeS RGB-N:** 53.21 (StitchFusion MiT-B4 + FFMs) and 54.59 (Swin-L-22k).
- **MCubeS RADN:** 57.62 (FuseAdapt, DINOv2-B).
- **MFNet:** 61.3 (Sigma-B), or 61.7 (a May-2026 non-peer-reviewed preprint).

### Cited Findings

**IDD-AW leaderboard (ICPR'24, split unstated; mIoU / SmIoU / SmIoU(tp))**

| Rank | Entry | mIoU | SmIoU | SmIoU(tp) |
|---|---|---|---|---|
| 1 | KweenCoders (RGB+N) | 68.32 | **64.73** | **55.07** |
| 2 | SixthSense (RGB) | **68.54** | 63.52 | 52.08 |
| 3 | SemSeggers (RGB+N) | 67.58 | 62.15 | 51.60 |
| — | Organiser baseline RGB+N | 67.30 | 62.26 | 51.50 |
| — | Organiser baseline RGB | 64.70 | 60.56 | 51.32 |

- SixthSense also reports **69.51 val mIoU**.
- Source: [ICPR report HTML, Table 2](https://arxiv.org/html/2409.05327v1)

**IDD-AW paper, InternImage-B (mIoU %, integers)**

| Input | Rain | Fog | Low-light | Snow | All |
|---|---|---|---|---|---|
| RGB | 62 | 64 | 62 | 53 | 64 |
| NIR | 61 | 58 | 57 | 51 | 61 |
| RGB+NIR | 66 | 65 | 63 | 53 | 67 |

- IDD-AW-all RGB model: mIoU 64, SmIoU(tp) 60, SmIoU 51.
- Source: [IDD-AW 2311.14459 HTML, Tables 2–3](https://arxiv.org/html/2311.14459)

**Other IDD-AW numbers**
- SegBridge (CVPR'26 Findings): **69.8 mIoU on IDD-AW val**. This is RGB-only continual TTA, not comparable as fusion. — [wave1_datasets_audit.md]

**MCubeS RGB-N and RGB-A-D-N (StitchFusion Table 3)**

| Variant | RGB-N | RGB-A-D-N |
|---|---|---|
| MiT-B4 | 51.67 | 51.74 |
| MiT-B4 + FFMs | **53.21** | 53.92 |
| Swin-L-22k | **54.59** | 55.96 |

Source: [StitchFusion HTML](https://arxiv.org/html/2408.01343)

**MCubeS RADN, other methods**

| Method | RADN mIoU |
|---|---|
| CMNeXt | 51.54 |
| MMSFormer | 53.11 |
| U3M | 51.69 |
| Any2Seg | 51.91 |
| MAGIC (as quoted by FuseAdapt) | 53.01 |
| **FuseAdapt** | **57.62** (DINOv2-B, seed 3407) |

- RGB-only references: CMNeXt 48.16; MMSFormer 50.44.
- Sources: [FuseAdapt page](https://devinxzhang.github.io/FuseAdapt-Page/); [wave1_rgbnir_perception_sota.md]; [wave1_datasets_audit.md]
- FuseAdapt missing-modality mean over the 15 input subsets on MCubeS:

  | Objective | Mean mIoU |
  |---|---|
  | Modality dropout only | 43.67 |
  | Output KD | 49.84 |
  | Global-feature KD | 50.57 |
  | Full PMM | 52.89 |

  Source: [FuseAdapt page](https://devinxzhang.github.io/FuseAdapt-Page/)

**MFNet (mIoU)**

| Method | Backbone | mIoU |
|---|---|---|
| CMX | MiT-B4 | 59.7 |
| CMNeXt | MiT-B4 | 59.9 |
| Sigma-T | VMamba-T | 60.2 |
| Sigma-S | VMamba-S | 61.1 |
| **Sigma-B** | VMamba-B | **61.3** |
| SGFNet | ResNet-152 | 60.1 |
| Canıtez & Erkent Nano (arXiv, May 2026, with TTA) | ConvNeXtV2-N | 61.7 |
| HAPNet | ConvNeXt-L | 61.2 |
| StitchFusion | MiT-B4 | 57.8 |
| StitchFusion | Swin-L-22k | 60.7 |

- The StitchFusion repo lists 57.80 / 57.91 / 58.13 checkpoints.
- Sources: [Frequency-Guided Fusion 2605.26273 HTML](https://arxiv.org/html/2605.26273); [StitchFusion HTML](https://arxiv.org/html/2408.01343); [StitchFusion README](https://github.com/LiBingyu01/StitchFusion)

**FMB (mIoU)**

| Method | Backbone | mIoU |
|---|---|---|
| SegMiF | MiT-B4 | 54.8 |
| MMSFormer | MiT-B4 | 61.7 |
| StitchFusion | MiT-B4 | 64.3 |
| StitchFusion | Swin-L | **68.7** |

The repo checkpoint scores 64.85. — [StitchFusion HTML](https://arxiv.org/html/2408.01343); [README](https://github.com/LiBingyu01/StitchFusion)

### Inferences

**Concrete targets to state in the paper**
- **IDD-AW val (475), same recipe, MiT-B2:**
  - Ours − StitchFusion-B2 ≥ **+1.0 mIoU** and ≥ **+1.5 SmIoU**.
  - Ours − SegFormer-B2 early fusion ≥ +2.
  - Largest gains in **Low-light and Rain** (where InternImage NIR-only is closest to RGB: 57 vs 62 and 61 vs 62), not in Snow, where NIR adds nothing (53 vs 53).
  - Absolute: B4/DINOv2 variant ≥ **70.0 val mIoU**, beating SixthSense's 69.51 val with far fewer parameters and no Cityscapes/IDD pretraining.
  - If IDD pre-training is used, it must be declared and applied to the baselines as well.
- **MCubeS RGB-N:**
  - Beat StitchFusion-B4+FFMs **53.21** with MiT-B4.
  - With the DINOv2-B host, target ≥ 54.6 (Swin-L, 54.59).
- **MCubeS RADN:** optional. FuseAdapt's 57.62 with DINOv2-B is the bar. Claim only "competitive" unless it is beaten.
- **MFNet:** generality only. Show ours > StitchFusion-B4 (57.8–58.1, same code) by ≥ +1. Do not claim SOTA versus Sigma-B's 61.3 unless reached.
- **Report the protocol difference explicitly.** Leaderboard numbers (ICPR) are on an unstated split, so put them in a separate "reported" block of the table, not in the controlled rows.

### Gaps
- No RGB-X fusion method (CMX, CMNeXt, StitchFusion, FuseAdapt) has IDD-AW numbers. You must produce all controlled baselines yourself; there is no external per-condition val baseline beyond integers.
- The ICPR split ambiguity (val vs test) is unresolved.
- MAGIC's 53.01 is second-hand.
- The 2605.26273 preprint uses TTA and is not peer-reviewed.

---

## Q4. Implementing C1: cross-fitted dense counterfactual utility supervising the gate (plus C3, label-free)

### Takeaway
1. **Fold models.** Train K=2 fold models (ideally 2 seeds each) with stochastic NIR dropout so that "NIR dropped" is in-distribution.
2. **Utility maps.** For every training image, from the fold model that did not train on it, compute u = CE(RGB, ∅) − CE(RGB, NIR) at H/4 in a single batched forward pass.
3. **Null.** Build a seed-disagreement null.
4. **Calibration.** Standardise u to z.
5. **Supervision.** Supervise per-stage gates with a calibrated soft target, using BCE as the main loss and soft Spearman and Huber as ablations.

Insert gates on the NIR stream in StitchFusion:
- at the stage outputs before `FeatureCross` in `stitchfusion.forward`;
- optionally on the NIR→RGB adapter injections in `Block_every_one`.

For CMNeXt, insert them on `x{k}_f` before `self.FRMs[k-1]`.

The cited "gate collapse" evidence (2604.10702) is a **prostate-MRI, backbone-conditional** result: nnU-Net collapses, Mamba does not. Reproduce the diagnostic on IDD-AW rather than citing it as general.

### Cited Findings
- **2604.10702** (Shu et al., arXiv, v4 June 2026; no venue): "Backbone-Conditional Behavior of Modality Gating in Multi-Modal Prostate MRI Segmentation".
  - On nnU-Net, gates "collapse into a near-static modality prior" (across-case SD 0.0033). On Mamba the SD is 0.0365.
  - Replacing gates with their training-set mean leaves nnU-Net unchanged but degrades Mamba.
  - "Modality dropout is the only component… beneficial on both backbones."
  - Datasets: PI-CAI (n=1500, 5-fold CV) and Prostate158.
  - Source: [arXiv 2604.10702](https://arxiv.org/abs/2604.10702)
- **Modality-dropout rates in prior work.** No standard rate exists for RGB-X segmentation. Examples:
  - TupleInfoNCE masks modalities on 0.6 of training batches;
  - the sensor-failure benchmark models each modality as failing i.i.d. Bernoulli(p) and reports expected mIoU over combinations;
  - another method samples the number of dropped modalities from a truncated geometric distribution.
  - Sources: [TupleInfoNCE 2107.02575](https://arxiv.org/pdf/2107.02575); [Sensor-failure benchmark 2503.18445](https://arxiv.org/pdf/2503.18445)
- **FuseAdapt uses "modality dropout only" as a baseline**, which scores far below PMM for missing modalities (43.67 vs 52.89). — [FuseAdapt page](https://devinxzhang.github.io/FuseAdapt-Page/)
- **Exact insertion points in StitchFusion** (`semseg/models/backbones/stitchfusion.py`):
  - In `stitchfusion.forward`, each stage runs `patch_embed` → `block[...]` → `norm`. The reshaped stream list `x_in` (index 0 = RGB, 1 = NIR) is then fused by `feature_cross(x_in, layer_idx=i)` and appended to `outs`.
  - In `Block_every_one.forward`, stream i is injected into stream j by `outputs[j] = xi + drop_path(adap_t_att(norm1(x_ori)))`, and likewise with `adap_t_mlp` after the MLP.
  - Source: [StitchFusion code](https://github.com/LiBingyu01/StitchFusion)
- **Exact insertion points in CMNeXt** (`semseg/models/backbones/cmnext.py`):
  - Per stage: `x{k}_cam, x{k}_f = self.FRMs[k-1](x{k}_cam, x{k}_f)` then `x_fused = self.FFMs[k-1](...)`.
  - With more than one auxiliary modality, a learned per-token `tokenselect` score (`extra_score_predictor`, `PredictorConv`) is applied. With a single NIR modality it is bypassed (`x_ext[0]`).
  - FRM in `modules/ffm.py` contains `ChannelWeights` and `SpatialWeights`, a learned spatial gate.
  - Source: [DELIVER code](https://github.com/jamycheung/DELIVER)
- **Differentiable soft ranking** for a Spearman-style loss: [torchsort](https://github.com/teddykoker/torchsort) (implements Blondel et al., ICML 2020 [mem]).

### Inferences

**C1 recipe (implementation-ready)**

1. **Folds.** Split the 3430 training images **by sequence** into K=2 folds, stratified by condition (rain, fog, low-light, snow). Sequence-level splitting is mandatory: adjacent frames leak.
   - K=3 is the ablation. It gives better fold models (67% vs 50% of the data) at 1.5× the cost.
2. **Fold-model training.** StitchFusion-B2, 20k iterations, same recipe as the main runs.
   - **Modality dropout per sample:** keep both 0.5, drop NIR 0.35, drop RGB 0.15. "Drop" means replacing the normalised stream with zeros, i.e. the dataset mean.
   - Train **2 seeds per fold** (4 models for K=2).
   - The fold model's accuracy on its held-out fold must be within about 3 mIoU of a full-data model. Otherwise u measures fold-model weakness.
3. **Utility computation** (`tools/compute_utility.py`):
   - For each held-out image, build a batch of 2: [(RGB, NIR), (RGB, 0)].
   - Take logits at H/4 (SegFormer head output, 256×192 for 1024×768). Compute per-pixel CE against labels nearest-downsampled to H/4, with ignore → NaN.
   - u_s(x) = CE_drop − CE_full for each seed s.
   - Store float16 `.npy`: about 98 KB per image, about 340 MB total.
   - Cost: 3430 × 2 seeds × 1 batched forward ≈ <1 GPU-h.
   - Compute **u_RGB** (drop RGB) alongside it; it is useful for symmetric gates and analysis.
4. **Seed-noise null and calibration:**
   - ū = (u_1 + u_2)/2, and the null difference n = u_1 − u_2.
   - Estimate σ_null(x) as a smooth function: bin pixels by (class of the GT, condition, CE_full decile) and take the robust SD (1.4826·MAD) of n/√2 in each bin.
   - z = ū/σ_null.
   - Target **y = Φ(z)**, the probability that NIR helps. This automatically shrinks noisy utilities to 0.5.
   - Report the fraction of pixels with |z| > 1.96 (non-triviality) and split-half reliability ρ(u_1, u_2) at superpixel level (SLIC, about 500 superpixels per image).
   - Pixels where both CEs are below 0.05 (confident and correct either way) get target 0.5 with weight 0, or are excluded.
5. **Gate head** (`semseg/models/modules/nir_gate.py`):
   - Per stage s, g_s = σ(MLP([LN(f_rgb), LN(f_nir), φ_s])). The MLP is a 1×1 conv over (C_s·2 + d_φ) → 64 → 1, followed by a 3×3 depthwise smoothing.
   - φ_s holds the C2 descriptors avg-pooled to stage resolution; omit them for C1-only.
   - Initialise the final bias to +3 so that g ≈ 0.95 and training starts at the baseline.
6. **Where to apply the gate:**
   - **(a) Stage-output gate** (default): `x_in[1] = g_s * x_in[1]` just before `feature_cross` in `stitchfusion.forward`. Optionally also add (1−g_s)·m_s, a learned "missing-NIR" token, so that g = 0 reproduces the dropout input distribution.
   - **(b) Deep gate** (ablation): multiply only the NIR→RGB adapter term in `Block_every_one`, i.e. the `outputs[0] += drop_path(adap(norm(x_ori_nir)))` update, by token-wise g_s.
   - **CMNeXt equivalent:** `x{k}_f = g_k * x{k}_f` before `self.FRMs[k-1]`.
7. **Gate loss:**
   - L = L_seg + λ Σ_s w_s·BCE(g_s, avgpool_s(y)), with λ = 0.1–0.5 swept on held-out train, w_s = 1, and pixel weights |2y−1|.
   - Ablations: (i) Huber regression of g on σ(ū/T); (ii) a per-image soft-Spearman loss via torchsort on 4096 random pixels; (iii) a hard sign target 1[ū > 0].
8. **Training the final model.** Train on all 3430 images with the out-of-fold y maps. u is needed only at training time, so inference cost is just the gate head and descriptors.
9. **Measuring u on val, for the gate–utility correlation:**
   - Train one extra "probe" pair on the *full* train set (a dropout model, 1 seed) and compute u_val with val labels.
   - Report Spearman ρ(g, u_val) per image (mean ± CI) and pooled, plus AUROC of g for sign(u_val).
   - **Regret rate** = P(fused wrong ∧ RGB-only correct) over valid pixels, per condition. Also report the image-level share where the fused mIoU < RGB-only mIoU.
10. **Collapse diagnostic** (motivating figure). For the learned-only gate, report:
    - across-image SD of the mean gate per stage;
    - Δ mIoU when g is replaced by its training mean;
    - ρ(g, u_val).

    The claim is supported if the learned gate is near-static and the supervised gate is not.

**C3 recipe: label-free utility head**
- **Teacher pseudo-labels.** On unlabelled RGB-NIR frames, take an EMA teacher's flip-TTA pseudo-labels ŷ at confidence > 0.9.
- **Label-free utility.** u_LF = CE(f(RGB, ∅), ŷ) − CE(f(RGB, NIR), ŷ), from the out-of-fold model, or a symmetric-KL disagreement weighted by teacher confidence.
- **Learned predictor.** Alternatively, train a small "utility head" h(features, φ) to regress z on labelled data, then use h as the teacher on unlabelled frames.
- **Validation.** Report ρ(u_LF, u) on val. Gate supervision on unlabelled data counts only if ρ ≥ 0.3.
- **Unlabelled pools:**
  - (i) FANVID (JAI FS-3200T; average NIR1 and NIR2 to approximate 700–1000 nm; weather variety; a different camera, so it doubles as a domain-shift test);
  - (ii) VNHD if obtained;
  - (iii) IDD-AW test-split images, **only** in a clearly marked transductive appendix experiment and only if no test-server evaluation is planned.

**The theory hook (one proposition):**
- Under log loss with the gate as a mixture weight, the Bayes-optimal gate is monotone in E[u | x].
- In-sample u is biased toward 0 because training CE → 0. Cross-fitting removes this bias, as in double ML (Chernozhukov et al., Econometrics J. 2018, arXiv 1608.00060 [mem]).
- Show this empirically: report in-sample vs out-of-fold u histograms.

### Gaps
- No prior RGB-X paper found with a dense per-pixel utility target, so there is no reference implementation. This is consistent with the wave1 novelty search, but the 2026 literature moves fast. Re-search arXiv for "counterfactual modality utility segmentation" at week 1 and week 10.
- The modality-dropout rates above are my choice. No standard rate was found for RGB-X segmentation.
- The 2604.10702 collapse result is from medical MRI and depends on the backbone. It does not show that RGB-X gates collapse; the paper must show this itself.

---

## Q5. Implementing C2 (physics reliability descriptors) and the IDD-AW-C per-band corruption suite

### Takeaway
Compute about 10 cheap per-pixel descriptor channels at H/4 on the GPU:
- per-band Poisson–Gaussian σ(x) from a per-image variance-vs-mean fit on flat patches;
- per-band local contrast-to-noise ratio (CNR);
- dark-channel transmissions for RGB and NIR, and the **implied Ångström exponent q̂**;
- the NIR/RGB ratio and an NDVI-like index;
- saturation/glare masks.

Build IDD-AW-C with spectrally correct haze, using the Kim visibility model β(λ) = (3.91/V)(λ/550)^−q:
- q ≈ 0.5–1.3 for haze at V = 1–50 km;
- q = 0 (wavelength-flat Mie) for fog at V < 0.5 km.

Also add per-band low light with Poisson–Gaussian noise, glare, and NIR-specific failures. Depth comes from a monocular metric-depth model.

### Cited Findings
- **Kim model:**
  - Attenuation β = (3.91/V)(λ/550 nm)^−q.
  - q = 1.6 for V > 50 km; 1.3 for 6–50 km; 0.16V+0.34 for 1–6 km; V−0.5 for 0.5–1 km; **0 for V < 0.5 km**, where fog attenuation is wavelength-independent.
  - Sources differ on whether the 0.16V+0.34 branch covers 1–6 km or all V < 6 km.
  - Sources: [Radioengineering 19(2) 2010](https://www.radioeng.cz/fulltexts/2010/10_02_223_227.pdf); [Aerial quantum communication review 2309.13417](https://arxiv.org/pdf/2309.13417)
- **Experimental check (Ijaz et al., PIMRC 2013).** Short visibilities contradict the Kim model, while wavelength dependence persists above about 15 m visibility. — [Northumbria record](https://nrl.northumbria.ac.uk/id/eprint/16216)
- **HSVF/VNHD** uses a single β and notes the "weak wavelength dependence" of Mie scattering. — [HSVF HTML](https://arxiv.org/html/2507.03893v1)
- **Foi et al.** (IEEE TIP 17(10):1737–1754, 2008): single-image Poissonian-Gaussian (a, b) estimation with clipping. MATLAB code ClipPoisGaus_stdEst2D v2.32 is at [Foi sensor-noise page](https://webpages.tuni.fi/foi/sensornoise.html).
- **Python PCA-based noise-level estimator** for Chen et al., ICCV 2015 [mem for paper details]: [zsyOAOA/noise_est_ICCV2015](https://github.com/zsyOAOA/noise_est_ICCV2015) (repo exists).
- **Dark-channel-prior Python implementation** (He et al., CVPR 2009 / TPAMI 2011 [mem]): [He-Zhang/image_dehaze](https://github.com/He-Zhang/image_dehaze) (repo exists).
- **JAI bands:** VIS 400–670 nm and NIR 740–1000 nm. No per-channel peak wavelengths are published. — [JAI](https://www.jai.com/products/fs-3200d-10ge)
- **IDD-AW: NIR is worse than RGB in fog** (58 vs 64) and equal in snow (51 vs 53, fused 53). — [IDD-AW HTML](https://arxiv.org/html/2311.14459)

### Inferences

**C2 descriptors** (`tools/physics_descriptors.py`; PyTorch on the GPU, computed in the dataloader worker or on the fly)
1. **Linearise.** Assume 8-bit PNG output is gamma-encoded [UV]. Apply an inverse sRGB curve and log the assumption. Also ablate "no linearisation".
2. **Noise.** Per band c ∈ {R, G, B, N}, per image:
   - Take 8×8 patches with low gradient energy (lowest 20% of Sobel energy). Fit var = a_c·mean + b_c by robust least squares (Huber), in the spirit of Foi 2008.
   - σ_c(x) = √(a_c·μ_c(x) + b_c), with μ_c from a 5×5 box filter.
   - Validate on 50 images against ClipPoisGaus run offline in MATLAB/Octave. Report the correlation of (a, b) in the supplement.
3. **Local CNR.** CNR_c(x) = std_7×7(G_1 * I_c)(x) / σ_c(x). Use log CNR. Add **ΔCNR = log CNR_N − log CNR_Y**, where Y = luminance. This is the most direct "NIR more informative here" cue.
4. **Differential attenuation (haze cue):**
   - Estimate airlight A_c from the top 0.1% of the dark channel.
   - Transmission per band group: t_RGB = 1 − ω·min_{c∈RGB} min_{Ω15}(I_c/A_c), with ω = 0.95; t_N likewise on NIR alone; refine with a guided filter.
   - Implied Ångström exponent q̂(x) = log(log t_RGB / log t_N) / log(λ_N/λ_vis), with λ_vis ≈ 550 nm and λ_N ≈ 850 nm [UV centres]. Clamp to [−0.5, 3].
   - Physics prediction to report: median q̂ is about 0 on FOG images and positive on hazy, distant regions.
   - Compare against the γ≈0 risk flagged in wave1.
5. **Ratios.** log((N+ε)/(Y+ε)) and (N−R)/(N+R+ε), the NDVI-like index; this is a vegetation and material cue.
6. **Glare and saturation.**
   - sat_c = 1[I_c ≥ 250/255], dilated 5 px.
   - Glare = high-luminance blob (Y > 0.9, area > 50 px at H/4) plus a radial falloff mask (Gaussian, σ = 3× blob radius).
   - NIR-only saturation (e.g., NIR headlight bloom) is a separate channel.
7. **MCubeS only.** Add `NIR_warped_mask` as a validity channel.
8. **Normalisation.** Standardise each channel with train-set statistics, then avg-pool to H/4, H/8, H/16 and H/32 for the per-stage gate heads. That is d_φ ≈ 12 channels.
9. **"Physics-only" baselines:**
   - (i) a heuristic gate g = σ(α·ΔCNR + β·q̂ − γ·sat_N) with α, β, γ fit by logistic regression to sign(u) on train — no deep learning;
   - (ii) the gate head with φ only and no image features.

**IDD-AW-C** (`tools/make_iddaw_c.py`; built on the **val** split only, with a fixed seed; release the code, not the images, unless the licence allows)
- **Depth.** Monocular metric depth for val images: Depth Anything V2 metric-outdoor [mem, arXiv 2406.09414]. Cache it. Clip d to [1, 300] m.
- **Band centres.** λ_B = 465, λ_G = 540, λ_R = 615, λ_N = 850 nm. These are assumed effective centres for 400–670 / 740–1000 nm bands [UV]. Ablate λ_N ∈ {800, 900}.
- **Corruptions** (5 severities each):

  | Corruption | Model | Severities 1–5 |
  |---|---|---|
  | **Haze-spectral** | I_c = J_c·t_c + A_c(1−t_c), t_c = exp(−β_c·d), β_c = (3.912/V)(λ_c/550)^−q(V), Kim q; A from the image's sky or bright region | V = 20, 10, 5, 3, 1.5 km (q = 1.3, 1.3, 1.14, 0.82, 0.58) |
  | **Haze-flat** (control) | same, with q = 0 | same V |
  | **Fog-Mie** | q = 0; denser | V = 400, 250, 150, 100, 60 m |
  | **Low-light-PG** | Scale linear intensity by k; add Poisson–Gaussian noise per band; NIR gets its own k_N (illuminant-dependent; LED-like means k_N < k_vis) and gain | k = 1/4 to 1/64; read noise σ_r = 2–8 DN; two variants: halogen-like (k_N = k) and LED-like (k_N = k/4) |
  | **Glare** | Add a veiling-glare PSF (sum of Gaussians) at a random bright location; clip at 255 | 5 intensities |
  | **NIR failures** | NIR-only Gaussian noise; dead rows; misregistration shift of 1–8 px; NIR dropout (all zeros) | 5 levels |
  | **Occluders** (wavelength-flat control) | rain streaks or snow particles drawn identically in all bands | 5 levels |

- **Metrics:** mean mIoU and SmIoU per corruption, a relative-robustness ratio (corrupted / clean mIoU), and "**gate sanity**". Gate sanity means mean g should increase with haze severity, stay flat under Haze-flat and Fog-Mie, and drop to about 0 under NIR failures.
- **Why this suite matters.** The Haze-spectral vs Haze-flat pair is the falsifiable physics test. A physics-informed gate should help on the first and not on the second. Purely learned gates have no such prior.
- **Protocol.** Calibrate severities so that RGB-only SegFormer-B2 loses about 5/10/20/30/40% mIoU across levels (ImageNet-C-style; Hendrycks & Dietterich ICLR 2019 [mem]). Freeze the parameters before evaluating your method.

### Gaps
- No published effective band centres or QE curves for the JAI FS-3200D/IMX252 bands were found. The λ values are approximations.
- The IDD-AW PNG gamma/linearity is unknown.
- Kim-model branch boundaries differ between sources, and the model is contested for short visibilities (Ijaz 2013).
- Monocular depth on adverse-weather images is itself degraded. Use depth from the clean-ish val images and report this limitation.
- Foi's tool is MATLAB; I found no maintained Python port. The PCA estimator (Chen 2015) is Gaussian-only.

---

## Q6. Full ablation table, statistical protocol, and compute budget

### Takeaway
Use a 3-seed, controlled, single-recipe design on IDD-AW val (MiT-B2 StitchFusion host):
- 1-seed exploratory runs at 20k iterations to triage;
- 3-seed confirmation at 40k for about 10 key rows;
- **paired cluster bootstrap over val sequences** with summed confusion matrices, B = 10,000;
- Holm correction across a pre-registered family of about 12 comparisons.

The total budget is about 650–800 GPU-hours [UV estimate]. That is roughly 4–5 weeks of wall-clock time on one 24 GB GPU and fits Nov→Mar.

### Cited Findings
- **FuseAdapt seeds.** Mean ± sample SD over 3 seeds (3407, 3408, 3409). It explicitly states "three seeds characterize variation; they do not establish formal statistical significance." — [FuseAdapt page](https://devinxzhang.github.io/FuseAdapt-Page/)
- **Reproducibility noise.** StitchFusion's own reproduction differs by "a few tenths" of mIoU. — [StitchFusion README](https://github.com/LiBingyu01/StitchFusion)
- **Small val subsets.** IDD-AW val per condition: Rain 120, Fog 154, Low-light 100, Snow 101 images. — [ICPR HTML](https://arxiv.org/html/2409.05327v1)
- **Sequence correlation.** Splits are sequence-disjoint, so frames within a sequence are correlated. — [IDD-AW HTML](https://arxiv.org/html/2311.14459)
- **Per-image confusion matrices.** `safe_iou.py` computes metrics from an accumulated confusion matrix, so a bootstrap must resample per-image confusion matrices, not per-image mIoUs. — [safe_iou.py](https://github.com/Furqan7007/IDDAW_kit)

### Inferences

**Main ablation table** (IDD-AW val; columns: mIoU, SmIoU (paper set), SmIoU-tp, R/F/LL/S mIoU, regret %, ρ(g, u_val), IDD-AW-C mean mIoU, params, GFLOPs)

| ID | Row name | What changes | Seeds |
|---|---|---|---|
| B0 | RGB-only SegFormer-B2 | — | 3 |
| B1 | NIR-only SegFormer-B2 | — | 1 |
| B2 | Early fusion 4-ch SegFormer-B2 | — | 3 |
| B3 | Late fusion (logit average of B0 and B1) | — | — |
| B4 | StitchFusion-B2 (no gate) | host | 3 |
| B5 | B4 + modality dropout | controls for the dropout regulariser | 3 |
| G1 | + learned gate (same head, L_seg only) | the "learned-only" gate | 3 |
| G2 | G1 + modality dropout | strongest learned-gate control | 3 |
| G3 | G1 with gate → training mean (static prior) | collapse diagnostic | eval-only |
| G-img | image-level gate (one scalar per image and stage), supervised by mean u | CAFuser/QMF-like granularity | 1 |
| G-cls | per-class gate (pooled u per predicted class) | granularity | 1 |
| U1 | gate supervised by **in-sample** u (no cross-fitting) | tests the cross-fitting claim | 1→3 |
| **U2** | gate supervised by cross-fitted u, K=2, null-calibrated Φ(z), BCE | **C1** | 3 |
| U3 | U2 with K=3 | K sensitivity | 1 |
| U4 | U2 without the null (raw sign(ū) target) | null calibration | 1 |
| U5 | U2 with 1 seed per fold (null from a split-batch proxy) | cost reduction | 1 |
| U6a/b/c | U2 loss: Huber / soft-Spearman / hard-sign BCE | loss choice | 1 |
| U7 | U2 with proxy targets: (a) entropy reduction H(RGB) − H(fused); (b) gradient saliency; (c) per-image Shapley broadcast | is a *dense counterfactual* necessary | 1 |
| U8 | gate placement: stage-out only / deep adapters only / both; stages {1,2} vs {3,4} vs all | placement | 1 |
| P1 | heuristic physics gate (logistic on φ, no learning in the network) | physics alone | 1 |
| P2 | learned gate with φ input, no u | **C2 alone** | 3 |
| **P3** | U2 + φ | **C1+C2** | 3 |
| P4 | P3 leave-one-out: −σ, −CNR, −q̂, −ratio, −glare | descriptor attribution | 1 each |
| S1 | P3 + C3 unlabelled (FANVID) | **C3** | 3 |
| S2 | S1 with u_LF only (no labelled u) | label-free limit | 1 |
| O1 | oracle gate g = 1[u_val > 0] (uses val labels) | upper bound; diagnostic only | eval-only |
| X1 | full method on CMNeXt-B2 host | host generality | 3 |
| X2 | full method on DINOv2-B + LoRA host | VFM generality | 3 |
| X3 | full method on StitchFusion-B4 | scale | 1→3 |

**Secondary tables**
- **MCubeS RGB-N:** B4 host vs ours at MiT-B4 and on the DINOv2 host, 3 seeds. The RADN row is optional.
- **MFNet:** StitchFusion-B4 vs ours, 3 seeds.
- **Missing-NIR or corrupted-NIR robustness** (IDD-AW-C NIR failures plus full NIR drop): compare with B4, G2 and P3.

**Statistical protocol**
1. **Seeds.** 3 per key row (e.g., 3407/3408/3409, to match FuseAdapt). Report mean ± sample SD per metric.
2. **Paired cluster bootstrap.**
   - Resample val **sequences** with replacement, B = 10,000.
   - Within each replicate, sum the per-image 26×26 confusion matrices for method A and method B *on the same resampled images*, and compute Δ = metric(A) − metric(B).
   - Use seed-averaged confusion matrices, i.e. sum over the 3 seeds. Alternatively, do a two-level bootstrap (resample seeds, then sequences) for a conservative CI.
   - Report the 95% percentile CI and a two-sided bootstrap p-value.
3. **Pre-registered family** (Holm–Bonferroni at α = 0.05; write it down in week 5, before final runs):
   - U2 vs G2 on mIoU-all;
   - P3 vs G2 on mIoU-all;
   - P3 vs B4 on mIoU-all;
   - P3 vs B4 on SmIoU-all;
   - P3 vs U2 on mIoU-all;
   - P3 vs B4 on each of the 4 conditions;
   - U2 vs U1;
   - P3 vs G2 on IDD-AW-C haze-spectral;
   - S1 vs P3.

   That is 12 tests. Everything else is "exploratory, uncorrected".
4. **Correlation claims.** For ρ(g, u_val), compute per-image Spearman, then bootstrap over sequences, and compare the learned gate vs the supervised gate.
5. **Minimum detectable effect.** Estimate it after week 2 from B4's 3-seed SD and the bootstrap SE, and state it. Expected: per-condition CIs on about 100 images will be wide (maybe ±1.5–3 mIoU [UV]), so per-condition claims should rely on the 3-seed consistency plus the all-val test.

**Compute budget** (GPU-hours on one 24 GB card; my estimates [UV])

| Block | Runs | h/run | Total |
|---|---|---|---|
| IDD-AW baselines (B0–B5, CMX, CMNeXt, MMSFormer, DFormer/GeminiFusion/Sigma: about 10 configs; 3 seeds for B0, B2, B4, B5, CMNeXt) | ≈ 20 | 8–12 | ~200 |
| Fold models (K=2 × 2 seeds × 20k iterations) + K=3 ablation (3 × 1) + probe | 8 | 4–5 | ~40 |
| Utility and descriptor precomputation | — | — | ~5 |
| Ablations, 1 seed at 20k (about 20 rows) | 20 | 4 | ~80 |
| Key rows, 3 seeds at 40k (G1, G2, U2, P2, P3, S1 and X1–X3, minus overlap; about 9 rows × 3) | 27 | 9–12 | ~280 |
| MCubeS RGB-N (B4 host + ours, 3 seeds; plus optional RADN) | 6–10 | 8–12 | ~90 |
| MFNet (2 × 3 seeds) | 6 | 6–8 | ~45 |
| IDD-AW-C inference (about 35 corrupted versions × 475 images × about 10 models) | — | — | ~10 |
| Efficiency, latency, visualisations | — | — | ~5 |
| **Total** | | | **~750 GPU-h** (≈ 31 GPU-days) |

Fit: Nov 3 → Mar 15 is about 130 days. At 60% GPU utilisation that is about 1,870 h available, so a 2.5× safety margin. Cutting K=3, the Sigma port and the MCubeS RADN saves about 100 h.

### Gaps
- Real per-iteration times and memory at 1024×768 for two-stream MiT-B2/B4 on the student's specific GPU must be measured in week 1.
- The number of val sequences is unknown; the cluster bootstrap needs it.
- Whether 40k iterations is enough for convergence at 3430 images is unverified. The ICPR #1 entry used 160k on IDD-AW, after 150k on IDD.

---

## Q7. Figures and tables that accepted A* RGB-X segmentation papers contain

### Takeaway
Accepted A* RGB-X papers consistently contain:
1. a paradigm-comparison teaser;
2. an architecture diagram;
3. a main SOTA table across 3–5 datasets with backbone and params;
4. a modality-combination table;
5. ablations (placement, density, hidden dimension);
6. a params/FLOPs (and latency) table;
7. per-class IoU;
8. qualitative grids;
9. t-SNE or feature visualisations;
10. for 2025–26 VFM/robustness papers: missing-modality tables over all subsets, seed-variance reporting, and a controlled "same encoder, same recipe" comparison.

Paper A should add gate/utility maps per condition, a gate-collapse diagnostic, and the IDD-AW-C physics-falsification plot.

### Cited Findings
- **StitchFusion** (ACM MM'25 oral; review score 6.5/10):
  - **Figures:**
    1. fusion paradigms (single, mapping, prompt, exchange, stitch);
    2. framework with MiT-B3 complementing FFMs;
    3. overall framework (encoder with MoA, FFM, MLP decoder);
    4. other encoder architectures (low-rank MoA);
    5. qualitative results on MCubeS;
    6. modality examples;
    7. adapter density variants;
    8. per-class IoU bars;
    9–10. t-SNE;
    11–13. supplementary qualitative results.
  - **Tables:**
    1. SOTA on NYUDv2, DeLiVER, MCubeS, SUN;
    2. FMB, PST900, MFNet;
    3. modality combinations (MCubeS, DeLiVER);
    4. stage placement;
    5. dense-connectivity variants;
    6. hidden-dimension sweep;
    7. parameter efficiency vs CMNeXt and MMSFormer;
    8. GFLOPs and params by modality count and resolution;
    9–12. dataset classes and per-class tables;
    13. params per backbone and stage.
  - Sources: [StitchFusion HTML](https://arxiv.org/html/2408.01343); [README](https://github.com/LiBingyu01/StitchFusion)
- **FuseAdapt** (NeurIPS'26):
  - a method figure (frozen shared backbone with per-sensor streams);
  - a controlled comparison table (Shared / Separate / FuseAdapt) with **3-seed mean ± SD** on MUSES with MiT-B0 and B2;
  - an "information path" ablation (dense-feature routing vs all-path routed LoRA vs FuseAdapt);
  - a missing-modality objective table (mean over all 15 nonempty subsets);
  - measured latency (H100, FP32, 20 warm-ups and 100 runs);
  - qualitative results for RGB vs RGB+LiDAR.
  - Source: [FuseAdapt page](https://devinxzhang.github.io/FuseAdapt-Page/)
- **IDD-AW** (WACV'24):
  - triplet examples per condition;
  - label-hierarchy and pixel-count figures;
  - tree-distance examples;
  - dangerous vs tolerable mispredictions;
  - a cross-dataset qualitative grid;
  - SmIoU-vs-mIoU distribution;
  - cross-dataset, condition-expert and class-wise tables.
  - Source: [IDD-AW HTML](https://arxiv.org/html/2311.14459)
- **Sigma (WACV'25), GeminiFusion (ICML'24), DFormerv2 (CVPR'25), CAFuser (RA-L'25), AnySeg:** I did not fetch their figure lists this session. From memory [mem/UV]:
  - Sigma: an accuracy-vs-FLOPs teaser and efficiency tables;
  - GeminiFusion: an efficiency-vs-accuracy comparison against TokenFusion;
  - CAFuser: per-condition MUSES results and condition-token visualisations;
  - AnySeg: missing-modality tables over all subsets.

### Inferences
**Proposed figure/table plan for Paper A (8–9 pages plus supplement)**
- **Fig. 1 (teaser):**
  - Left: one fog frame and one low-light frame, each showing RGB, NIR, the counterfactual utility map u (red = NIR helps, blue = NIR hurts), the learned gate (near-constant) and our gate.
  - Right: a bar chart of mIoU per condition, baseline vs ours.
- **Fig. 2 (method):**
  - (a) the cross-fitting pipeline: folds → fold models with dropout → ΔCE maps → seed null → Φ(z) targets;
  - (b) the dual-stream host with per-stage gates and the physics-descriptor branch;
  - (c) the C3 unlabelled branch.
- **Fig. 3 (motivation and diagnostic):** histograms of in-sample vs out-of-fold u, and the gate SD across images for learned vs supervised gates (the collapse diagnostic, G3).
- **Fig. 4 (physics falsification):**
  - Mean g and Δ mIoU vs severity for Haze-spectral vs Haze-flat vs Fog-Mie vs NIR-failure on IDD-AW-C.
  - Plus a q̂ histogram per real IDD-AW condition: fog ≈ 0, low-light undefined, and so on.
- **Fig. 5 (qualitative grid):** 4 conditions × [RGB | NIR | GT | RGB-only | StitchFusion | Ours | g map | error map with safe-critical errors highlighted].
- **Fig. 6 (scatter):** g vs u_val, binned, with Spearman ρ and AUROC; learned vs supervised gate.
- **Tab. 1 (main IDD-AW val):**
  - all controlled baselines (3 seeds, mean ± SD) on mIoU, SmIoU (both sets), per-condition and regret;
  - plus a separate "reported, different protocol" block: ICPR leaderboard and InternImage-B.
- **Tab. 2 (generality):** MCubeS RGB-N (and RADN), MFNet, and optionally FMB, with backbone and params.
- **Tab. 3 (main ablation):** rows B4, G1, G2, U1, U2, P2, P3, S1, with Holm-adjusted p-values and bootstrap CIs.
- **Tab. 4 (granularity and placement):** G-img, G-cls, U8 variants and loss variants.
- **Tab. 5 (robustness):** IDD-AW-C mean mIoU per corruption family, plus missing NIR.
- **Tab. 6 (efficiency):** params, GFLOPs at 1024×768 and latency (batch 1, FP16 and FP32, 20 warm-ups and 100 runs, as FuseAdapt does) for SegFormer-B2, CMX, CMNeXt, StitchFusion, ours, and the descriptor overhead.
- **Supplement:**
  - Safe-mIoU implementation notes, including the script bugs and both important sets;
  - per-class IoU and safe IoU;
  - IDD-AW-C parameters;
  - the full fold protocol;
  - extra qualitative results;
  - the seed table;
  - the transductive C3 appendix.

### Gaps
- I did not verify the figure and table inventories of Sigma, GeminiFusion, DFormerv2, CAFuser or AnySeg this session (search budget). Those items are from memory.

---

## Q8. Week-by-week timeline (Nov 2026 → ACM MM 2027 / NeurIPS 2027 / AAAI-28) with go/no-go checkpoints

### Takeaway
Target **ACM MM 2027 (about Apr 1, 2027 [UV deadline])** as the first submission, with go/no-go checks at weeks 3, 5, 7 and 12.
- Use **NeurIPS 2027 (about mid-May [UV])** if the theory and the VFM host mature.
- **AAAI-28 (about late July [UV])** is the fallback, incorporating MM reviews.

The critical path is IDD-AW labels → controlled baselines → fold utilities → supervised gate.

### Cited Findings
- **Baselines are slow to train.** The ICPR #2 Mask2Former needed about 2 + 2 days on a DGX A100, and #1 needed 150k + 160k iterations on 2× V100. Both are infeasible to replicate within budget, which confirms "cite, do not retrain". — [ICPR HTML](https://arxiv.org/html/2409.05327v1)
- **Reproduction quirks.** StitchFusion checkpoints and code have known issues: issue #6, and `Block_shared` vs `Block_every_one`. Budget time for reproduction. — [StitchFusion README](https://github.com/LiBingyu01/StitchFusion)
- **A capstone repo is starting the same space.** krnRaj03/iddaw-safe-segmentation (RGB–NIR fusion plus a hierarchy-aware loss on IDD-AW; updated 2026-10-27) is a scooping signal for IDD-AW fusion. — [repo](https://github.com/krnRaj03/iddaw-safe-segmentation)

### Inferences

**Week-by-week plan (week 1 starts Mon Nov 2, 2026)**

| Wk | Dates | Work | Exit criterion / Go-No-Go |
|---|---|---|---|
| 1 | Nov 2–8 | Register and download IDD-AW, MCubeS and MFNet. Email HSVF authors for VNHD. Write the label converter and `eval_iddaw.py` (patched safe IoU, per-image confusion matrices). Set up StitchFusion and DELIVER environments. Measure memory and speed at 1024×768. | Masks visually verified; 3430/475 counts; a test eval of a random predictor gives about 1/26 IoU |
| 2 | Nov 9–15 | B0, B2 and B4 (1 seed, 40k). Early sanity check vs InternImage-B (RGB 64, fused 67; different backbone, so expect ±3). | B4 ≥ B0 + 1.5 mIoU (fusion helps on IDD-AW at all) |
| 3 | Nov 16–22 | Fold models (K=2 × 2 seeds, 20k) with dropout; compute u, the null and z; probe model for u_val. | **GO #1:** (i) ≥ 15% of valid pixels have \|z\| > 1.96; (ii) split-half superpixel ρ(u_1, u_2) ≥ 0.3; (iii) mean ū differs by condition in the predicted direction (LL/Rain > Fog/Snow); (iv) out-of-fold ū clearly differs from in-sample u, which is about 0. **If (i) or (ii) fails:** increase to K=3, use superpixel-level targets, or pivot to region-level utility. |
| 4 | Nov 23–29 | Gate head plus insertion (stage-out). G1 and G2 (1 seed). Collapse diagnostic G3. Start B5, CMX and CMNeXt baselines in the background. | Gate code is unit-tested: g ≡ 1 reproduces B4 within noise |
| 5 | Nov 30–Dec 6 | U1, U2 (1 seed), λ sweep, U4. Write the pre-registered test family. | **GO #2:** U2 − G2 ≥ +0.8 mIoU (1 seed, confirmed with a 2nd seed), OR ≥ +1.0 SmIoU, OR ρ(g, u_val) doubles with Δ mIoU ≥ +0.5. **If no:** reframe as an analysis or benchmark paper (C4 + collapse diagnostic + physics) for a Q1 journal, or AAAI with a lower bar. |
| 6 | Dec 7–13 | C2 descriptors; descriptor sanity (q̂ per condition, CNR maps); P1 and P2. | Descriptors computed in ≤ 15 ms per image on the GPU |
| 7 | Dec 14–20 | P3 (1 seed), P4 leave-one-outs. | **GO #3:** P3 − U2 ≥ +0.3 mIoU on all, or ≥ +1.0 on IDD-AW-C haze-spectral, or the gate shows the physics-predicted behaviour. **Else:** keep C2 as an analysis section only. |
| 8–9 | Dec 21–Jan 3 | Buffer (holidays). Build IDD-AW-C: depth caching, corruption code, severity calibration on B0. Queue 3-seed runs of B0/B2/B4/G2/U2 overnight. | Severity calibration frozen |
| 10 | Jan 4–10 | MCubeS RGB-N: StitchFusion-B4 reproduction (target about 51.7 / 53.2) and ours. | Reproduction within 1 mIoU of the paper; otherwise compare only to our own reproduction |
| 11 | Jan 11–17 | MFNet (B4 reproduction about 57.8 and ours). MCubeS RADN if time allows. | — |
| 12 | Jan 18–24 | C3: u_LF on FANVID, S1 and S2. | **GO #4:** ρ(u_LF, u_val) ≥ 0.3 and S1 ≥ P3 + 0.3. **Else:** C3 goes to the appendix and the title drops "semi-supervised". |
| 13–14 | Jan 25–Feb 7 | Host generality: X1 (CMNeXt), X2 (DINOv2-B + LoRA), X3 (B4). Remaining baselines (MMSFormer, DFormer v1, GeminiFusion or Sigma, time-boxed at 3 days each). | — |
| 15–16 | Feb 8–21 | All 3-seed key rows; IDD-AW-C evaluation; bootstrap and Holm; efficiency measurements. | Results freeze on Feb 21 |
| 17–18 | Feb 22–Mar 7 | Figures 1–6, qualitative grids, gate maps; first full draft. | Internal review |
| 19–21 | Mar 8–Apr 1 | Revise, supplement, code-release prep, anonymisation. **Submit to ACM MM 2027.** | — |
| 22–27 | Apr–mid May | NeurIPS 2027 option: add the formal proposition (optimal gate is monotone in E[u\|x]; cross-fitting unbiasedness), a broader RGB-X evaluation (FMB, DeLiVER RGB-D) and the VFM-host emphasis. | Only if the MM draft is strong and new results add ≥ 1 dataset |
| 28–38 | May–late Jul | AAAI-28: incorporate MM reviews (MM notifications are typically around July [UV]). | — |

**Risk register** (my assessment)
1. **Gains under 0.8 mIoU.** Mitigate with SmIoU, regret and robustness metrics, plus the collapse-diagnostic narrative.
2. **u is noisy.** Mitigate with region- or superpixel-level targets and Φ(z) shrinkage.
3. **Physics is vacuous in fog and snow.** This is predicted. Present it as a falsification success via the Haze-flat and Fog-Mie controls.
4. **Scooping by the capstone or FuseAdapt follow-ups.** Post an arXiv preprint at submission.
5. **IDD-AW licence.** Check that derived IDD-AW-C *code* (not images) can be released.

### Gaps
- Exact 2027 deadlines for ACM MM, NeurIPS and AAAI are not announced or verified. The dates are the caller's approximations.
- The real training throughput on the student's GPU is unknown until week 1, so all wall-clock estimates are [UV].
