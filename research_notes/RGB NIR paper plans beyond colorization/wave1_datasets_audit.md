## Dataset audit: RGB–NIR (silicon, 700–1000 nm) resources for non-colorization papers

The web-search quota ran out near the end. Rows marked **(mem)** come from memory and are unverified. Everything else was checked against the primary PDFs, GitHub repos or dataset pages.

### 1. IDD-AW
| Item | Verified facts |
|---|---|
| Size and conditions | 5,000 RGB–NIR pairs from 202 drive sequences in India (urban, rural, hilly). Rain 1500, Fog 1500, Low-light 1000, Snow 1000. |
| Sensor | JAI FS-3200D-10GE, a 2-CMOS prism camera. VIS 400–670 nm, NIR 740–1000 nm. It is co-axial, so pixel alignment comes from the optics. Frames were paired by timestamp and checked by hand. The paper does not mention NIR illumination, so night NIR is probably passive (unverified). Native resolution is not stated; models were trained at 1024×768. |
| Labels | Uses the IDD label hierarchy: 7 level-1 labels up to 30 level-4 labels. Evaluation uses 26 level-3 classes (toolkit `info.json`). Masks are generated with IDD's AutoNUE `createLabels` (`createLabels_iddaw.py`), which builds them from IDD-style JSON polygons. The project page says the data is annotated for semantic and instance segmentation, so instance masks and vehicle/person boxes should be derivable. Confirm `*_polygons.json` files are in the download. |
| Splits | The ICPR-24 / public split is 3430 train / 475 val / 1095 test. Per condition (train/val/test): Rain 1062/120/318, Fog 1033/154/313, Low-light 684/100/216, Snow 651/101/248. The test set was released anonymized with conditions merged, and its ground truth was held back. Later papers evaluate on the 475 val images. The GitHub kit has a different internal split (3926 / 1072). Whether test labels are public now is unverified; download requires IDD registration and a 24 h token. |
| Safe mIoU | Tree distance d(c,s) = shortest path in the label tree ÷ 2; n = number of levels. For each class, I_c^safe = IoU_c − Σ_s (d/n)·|gt_c∩pred_s| / |gt_c∪pred_c|. For an important class, the sum runs over all s ≠ c; for other classes, only over s in the important set. SmIoU = mean of I_c^safe, with range [−1, 1]. In the paper the default important set is traffic participants ∪ roadside objects. The released `safe_iou.py` hard-codes only the 8 traffic-participant classes. The paper is also inconsistent on td(person, rider): 1 in the text, 2 in the figure. |
| WACV baselines (InternImage-b, mIoU %, Rain / Fog / LL / Snow / All) | RGB 62/64/62/53/**64**. NIR 61/58/57/51/**61**. Early-stacked RGB+NIR 66/65/63/53/**67**. Cross-dataset: Cityscapes-trained 46, ACDC-trained 48, IDD-trained 54. SmIoU (all): mIoU 64, SmIoU(tp) 60, SmIoU 51. Snow condition expert: 53 / 43 / 28. InternImage-s (supplementary): RGB 63, NIR 60, RGB+NIR 66. |

**Papers that used IDD-AW** (29 citing works on Semantic Scholar plus web search):
- **ICPR 2024 competition report** (arXiv 2409.05327), given as mIoU / SmIoU / SmIoU(tp):
  - 1st, KweenCoders: FusionNet RGB-NIR fusion as preprocessing, then SegFormer: **68.32 / 64.73 / 55.07**.
  - 2nd, SixthSense: Mask2Former Swin-L, RGB only: 68.54 / 63.52 / 52.08.
  - 3rd, SemSeggers: DarkVisionNet smoothing on RGB-NIR, then Mask2Former: 67.58 / 62.15 / 51.60.
  - Organisers' baselines: RGB+NIR 67.30 / 62.26; RGB 64.70 / 60.56.
  - These SmIoU values do not match the scale of the WACV paper's SmIoU (51), so don't compare across the two papers.
- **Pix2Next** (MDPI *Technologies* 2025, arXiv 2409.16706): RGB→NIR translation on IDD-AW 3430/475. PSNR 30.41, SSIM 0.923, FID 32.71.
- **SegBridge**, "Continual Adaptation of VFMs…" (CVPR 2026 Findings): RGB-only continual test-time adaptation. 69.8 mIoU on IDD-AW val, against 58.4 for the IMP baseline.
- **Uses RGB only**: ICVGIP 2025 semantic-guided weather removal (NIQE/BRISQUE on IDD-AW); AWARE (IEEE Access 2026, merged IDD + IDD-AW).
- **Low-tier venues, numbers unverified**: UGF-Net (WcCST 2026, uncertainty-guided RGB-NIR fusion) and "Color Space Fusion…" (ICNTE 2026, reports mIoU, SmIoU and mAP50).
- **Main finding**: no A* or Q1 paper has yet proposed and benchmarked an RGB-NIR fusion method on IDD-AW. Nobody reports detection or instance results on it either.
- **Mislabelled modality**: both the IDD-AW paper and Pix2Next call KAIST multispectral "NIR", and IDD-AW also lists MSRS as NIR+RGB. KAIST is thermal (LWIR); MSRS appears to be infrared–visible fusion data, i.e. thermal (mem). Don't copy these labels.

### 2. FANVID and FMSVD
FANVID is **not** from Niu et al. Your guess was wrong; corrected attributions below.

| Resource | Key facts | Labels | Availability | Later use |
|---|---|---|---|---|
| **FANVID**, "Object-Aware NIR-to-Visible Translation" (Gao, Gu, Liu, Fu, ECCV 2024) | 5,144 sets at 2048×1536 from a JAI FS-3200T 3-CMOS prism camera. Each set has VIS 400–700, NIR1 700–800 and NIR2 820–1000 nm images, aligned through the prism. Content: streets, parks, traffic, campuses, in sunny, cloudy, rainy and snowy weather. Night or low-light content is not mentioned. Split 4502 / 642. | Segmentation masks are Mask2Former pseudo-labels (.npy), not human labels. 40 test images were hand-annotated for detection rates only. | OneDrive and Baidu links on GitHub `Yiiclass/Sherry`; no license stated. | Paper's method: 25.57 / 23.37 dB PSNR (NIR1 / NIR2). HAQAGen (arXiv 2601.01103) uses its own 4100/514/530 split. |
| **FMSVD**, "NIR-assisted Video Enhancement via Unpaired 24-hour Data" (Niu et al., ICCV 2023) | 64 videos, 41,363 frames at 240×320; 29 day and 35 night scenes. JAI FS-3200T, with 850 nm LEDs as active illumination at night. | No paired low-light / normal-light ground truth; evaluated with PI and NIQE. | GitHub `MyNiuuu/NVEU` | — |

HAQAGen is by Attri, Dwivedi, Das and Kurmi at IISER Bhopal, so it is probably your group's own paper. It describes FANVID as "faces & urban" and "700–800 nm"; that contradicts the ECCV paper and is worth correcting.

### 3. EPFL and VCIP2020
| Resource | Key facts | Labels | Availability | Other tasks |
|---|---|---|---|---|
| **EPFL RGB-NIR Scene** (Brown & Süsstrunk, CVPR 2011) | 477 pairs at 1024×768 TIFF. Shot as separate exposures on modified Nikon D90 / Canon T1i cameras, split around 750 nm, registered with SIFT + RANSAC. About 50 pairs are misaligned on moving content (FANVID paper). | 9 scene categories: country, field, forest, indoor, mountain, oldbuilding, street, urban, water. | Free zip; RAW available on request. | Scene recognition (the original task). Dehazing, fusion, cross-spectral matching and denoising (mem). |
| **VCIP2020 NIR colorization challenge** | **Built from the EPFL images**: 372 registered 256×256 training pairs, 18 validation pairs and 1,020 unpaired online RGB images. Test set is 28 pairs as reported by later papers. Categories: countryside, forest, field, mountain. | None | Google Drive (challenge page) | Best verified: HAQAGen 24.96 PSNR, ColorMamba 24.56. |

**Do not count EPFL and VCIP2020 as independent benchmarks**; they share the same images.

### 4. Hyperspectral sources for physically simulating RGB+NIR
| Dataset | Range / bands | Images, resolution | Semantic labels | Availability |
|---|---|---|---|---|
| **ICVL** (Arad & Ben-Shahar, ECCV 2016) | RAW ENVI files: 519 bands, 400–1000 nm, ~1.25 nm steps. The .mat files are only 31 bands, 400–700 nm. | 202 scenes at 1392×1300 | No | **Yes**, on Hugging Face `ICVL-BGU/ICVL_HS_2016` (gated, ~335–405 GB). License CC BY-NC-ND 4.0, which may limit releasing derived data. RAW files are uncalibrated camera counts. |
| **Hyperspectral City V2** (PBDL 2021) | 128 bands, 450–950 nm | 1,330 images at ~1422×1889 (1054 train with coarse labels; test with fine labels) | 19 Cityscapes classes | **Server is live** (isis-data.science.uva.nl/cv/HyperspectralCityV2.0/). Data were H.264-compressed (lossy). Non-commercial. |
| **HSI-Drive v2** (SSCI 2023, arXiv 2411.17530) | 25 bands, 600–975 nm. No blue or green, so RGB cannot be simulated. | 752 images at 409×216 | 9–10 classes | Password by email |
| **HyKo2** (ICCV Workshops 2017) | VIS sensor 15 bands 470–630 nm; NIR sensor 25 bands 600–975 nm (mem). The two cameras are separate, not co-registered. | 371 labelled VIS images | 10 classes | Koblenz server |
| **TokyoTech 59-band** | 420–1000 nm at 10 nm, reflectance | 16 scenes at 512×512 | No | Public. Attribution to Monno et al. unverified. |
| **LIB-HSI** (ECCV Workshops 2022, arXiv 2212.02749) | Specim IQ, 204 bands. The camera covers 400–1000 nm; the delivered range was not checked. | 513 images at 512×512 | 44 façade material classes | CSIRO portal, CC BY-NC 4.0 |
| **Hyper-Drive** (arXiv 2308.08058) | 660–1700 nm | More than 500 labelled cubes | ATLAS ontology terrain labels | GitHub / Hugging Face |
| KAUST-HS / CAVE / Harvard / ARAD-1K | About 400–700/730 nm only | 409 / 32 / 77 / 1000 | No | Not usable for NIR |

I could not identify a dataset called "H2". HS3-Bench (IROS 2024, arXiv 2409.11205) found that extra RGB training data helps more than extra spectral bands.

### 5. Spectral libraries
- **USGS splib07** (Kokaly et al., USGS Data Series 1035, 2017; CC0). Instrument coverage overall is 0.2–200 µm: Beckman 0.2–3 µm, ASD 0.35–2.5 µm, Nicolet 1.12–216 µm, AVIRIS 0.37–2.5 µm. Chapters: Minerals, Soils/mixtures, Coatings, Liquids, Organics, **Artificial materials**, Vegetation. Comes with versions convolved to ASD, AVIRIS and other sensors.
- **ECOSTRESS v1.0** (Meerdink et al., *Remote Sensing of Environment* 2019; includes ASTER v2): more than 3,400 spectra. **72 man-made** spectra (concrete, paving asphalt, brick, paint, roofing, glass, metal, rubber, tar), of which 45 start at ≤0.45 µm and so cover VIS–NIR.
- **RELAB** (Brown University; mostly minerals and meteorites) (mem).
- Neither library has much road-scene material. Asphalt, concrete, paint, vegetation and fabric reflectance in 400–1000 nm has to be assembled from ECOSTRESS plus USGS chapter A.

### 6. Other RGB+NIR datasets with task labels
| Dataset | Modalities, size | Labels | Conditions | Availability | Best verified results |
|---|---|---|---|---|---|
| **MCubeS** (Liang et al., CVPR 2022) | RGB, AoLP, DoLP, NIR; 500 sets from 42 scenes at 1224×1024; split 302/96/102 | 20 material classes plus semantic labels | Daytime | Public | CMNeXt (CVPR 2023): RGB 48.16, RGB-A-D-N 51.54. MMSFormer (IEEE OJSP 2024): RGB 50.44, all four 53.11. StitchFusion (ACM MM 2025, arXiv 2408.01343): **RGB-N 51.67 / 53.21 (with feature-fusion modules) / 54.59 (Swin-L)**. All four modalities: 53.92 (MiT-B4). With Swin-L, RGB-A-D (56.94) beats RGB-A-D-N (55.96), so adding NIR can hurt. |
| **RANUS** (Choe et al., RA-L 2018) | About 40k RGB-NIR pairs; 4,519 aligned 512×512 pairs over 50 sessions (Pix2Next). Separate cameras (mem). | 10 classes (mem); Pix2Next also used object annotations | Urban daytime | Old Google Site now needs a login, so availability is unclear | Pix2Next detection with Co-DETR: mAP 0.335 |
| **Freiburg Forest** (ISER 2016) | RGB, NIR, depth, NDVI, EVI at 1024×768; more than 15k raw frames | 6 classes; 366 labelled images, 230/136 split (mem) | Forest | Non-commercial | — |
| **Real-NAID** (Xu et al., IEEE TMM 2025, arXiv 2404.08514) | 100 static scenes × 3 ISO levels (4000, 12000, 32000), clean reference at ISO 600, 2048×2160. Huawei surveillance camera with active NIR (750–1100 nm). Split 70/30. | Paired clean RGB ground truth | Low light | GitHub `ronjonxu/NAID` | NIR-Restormer: 26.22 / 25.51 / ~24.76 dB (low / mid / high noise) |
| **DVD** (DarkVisionNet, **AAAI 2022**, not CVPR) | 307 pairs at 1792×1008 (267/40 split); noise is synthesised on clean RAW | Ground truth | Static scenes | Used by NAID; Niu et al. called it unreleased in 2023 | — |
| **Pixel-aligned RGB-NIR stereo** (Kim & Baek, arXiv 2411.18025; venue unverified) | Dichroic-prism RGB and NIR stereo plus LiDAR, active NIR; 43 scenes, 80k frames; 2,238 synthetic scenes | Depth only | Varied lighting | Stated public | — |
| **TAS-NIR** (IROS 2022 PPNIV workshop) | 209 VIS+NIR images from two stacked cameras | Fine-grained off-road semantic labels | Off-road | mucar3.de | — |
| PittsStereo-RGBNIR | About 42k stereo pairs at 582×492 | Material-aware disparity (mem) | — | — | — |

Only briefly relevant (from memory): MS2 (RGB, NIR and thermal; 195k frames; depth labels); NIR-VIS face sets (CASIA NIR-VIS 2.0, LAMP-HQ, Oulu); WeedMap and SEN12MS (agriculture and remote sensing, out of scope). I found **no verified RGB-NIR low-light detection dataset**.

### Implications
1. **A* segmentation/detection suite.** Use IDD-AW as the core: it is the only real-weather, prism-aligned RGB-NIR set with dense labels, and its fusion space is open; the best fusion entry so far is a 2024 competition preprocessing trick at about 68 mIoU. Add MCubeS, which has an established fusion leaderboard and lets you test cross-domain claims, plus RANUS (if obtainable) and Freiburg Forest or TAS-NIR. That makes four datasets spanning weather, materials, urban and off-road scenes.
   - Detection or instance work on IDD-AW needs boxes derived from the polygons; confirm the polygons are in the download first.
   - Report both mIoU and SmIoU from the released code, and state which important-class set you used.
   - Note that SmIoU values in the WACV paper and the ICPR report are not comparable with each other.
2. **Physics-based augmentation or simulation (good fit for a Q1 journal).** Simulate RGB+NIR with JAI-like sensor responses from ICVL RAW (400–1000 nm), HCV2 (450–950 nm, labelled) and TokyoTech (reflectance), using ECOSTRESS/USGS spectra for materials. Test on IDD-AW. Check that ICVL's ND licence permits releasing derived data.
3. **NIR-assisted low-light restoration.**
   - Paired ground truth: only Real-NAID (real noise) and DVD (synthetic noise).
   - Unpaired real night video with active 850 nm NIR: FMSVD.
   - IDD-AW low-light (1000 images, passive NIR, no clean ground truth) suits task-driven evaluation, i.e. restoration judged by downstream segmentation.
   - FANVID and EPFL/VCIP have no low-light ground truth and are colorization data only.
