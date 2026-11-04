# Paper 1 (A* conference): Label-free NIR plug-in for frozen RGB foundation segmenters

Target: NeurIPS 2027 (fallback AAAI-28, then CVPR 2028). Shared context, calendar, gates and venues: [../common/FULL_REPORT.md](../common/FULL_REPORT.md).

## ALT-1′ scores a 6 that strong numbers could lift to 7

### Every ingredient has precedent, but the combination does not

The arXiv sweeps found **no paper doing open-vocabulary or zero-shot segmentation from NIR or RGB+NIR, and none evaluating open-vocabulary segmentation on IDD-AW**. Every ingredient, however, has a close neighbour ([alternatives audit](notes/alternative_astar_ideas.md)). The literal ALT-1, which aligns NIR to DINOv2 and then segments from it, scores only 5/10. The obvious attack is "SpectraDINO plus Talk2DINO, applied to NIR".

The re-scoping to ALT-1′ adds the one piece the audit identified as a real technical hole: the *teacher-degradation problem*. A NIR student trained to match RGB-teacher features on foggy or dark pairs learns to copy the degraded features, so it adds nothing exactly where NIR should help. Distilling only where the teacher is reliable lets the student exceed the teacher in degraded regions. The paper stands or falls on showing this.

| Closest prior work | What it already does | What ALT-1′ adds |
|---|---|---|
| Gupta, Hoffman & Malik, cross-modal distillation, CVPR'16 ([arXiv 1507.00448](https://arxiv.org/abs/1507.00448)) | Transfers a labeled modality's representation to an unlabeled paired modality | Must cite as the paradigm's origin; ALT-1′ adds frozen-VFM segmenters, reliability masking and test-time fusion |
| ImageBind, CVPR'23 ([arXiv 2305.05665](https://arxiv.org/abs/2305.05665)); OpenESS, CVPR'24 ([arXiv 2405.05259](https://arxiv.org/abs/2405.05259)); OVOSE ([arXiv 2408.09424](https://arxiv.org/abs/2408.09424)) | Bind depth/thermal (ImageBind) or events (OpenESS: 53.93 / 43.31 mIoU on DDD17 / DSEC with no event labels) to image foundation models | Same recipe, but for NIR on real adverse-weather data; not novel as a paradigm |
| **SpectraDINO**, arXiv May 2026 ([HTML](https://arxiv.org/html/2605.02258)) | Frozen DINOv2 teacher. Shared-ViT student with modality stems and adapters. CLS cosine, symmetric InfoNCE, patch alignment and neighbourhood-KL losses. 100K pairs each from MS2, RASMD and IR500K. MCubeS RGB+NIR 46.76 (ViT-L) | No masking, no IDD-AW, no open vocabulary, no plug-in to a trained segmenter. It is the strongest baseline and the most likely reviewer comparison |
| Talk2DINO, ICCV'25 ([arXiv 2411.19331](https://arxiv.org/abs/2411.19331)); dino.txt ([arXiv 2412.16334](https://arxiv.org/abs/2412.16334)) | Text alignment of DINOv2 for open-vocabulary segmentation | Used off the shelf; creates the "composition" risk |
| MM-OVSeg, CVPR'26 ([arXiv 2603.17528](https://arxiv.org/abs/2603.17528)) | Optical–SAR fusion for open-vocabulary segmentation under adverse weather | The same narrative in remote sensing; ALT-1′ is driving data with NIR, label-free |
| Vireo, NeurIPS'25 ([HTML](https://arxiv.org/html/2506.09881)) | Open-vocabulary domain-generalised segmentation (OV-DGSS) with frozen VFMs plus *predicted* depth | No IDD, NIR or thermal target; NIR is a measurement, while depth is a function of RGB |
| SegBridge, CVPR'26 Findings ([supplement](https://openaccess.thecvf.com/content/CVPR2026F/supplemental/Jangamreddy_Continual_Adaptation_of_CVPRF_2026_supplemental.pdf)) | RGB-only continual adaptation of DINOv2 + Mask2Former; IDD-AW val 58.4 → 69.8 | NIR unused |
| Night-TTA, IEEE TAI ([arXiv 2307.04470](https://arxiv.org/abs/2307.04470)); masked mutual learning, ICRA'25 ([arXiv 2505.06951](https://arxiv.org/abs/2505.06951)) | RGB-T test-time adaptation at night (source trained on both modalities); masks uncertain regions in RGB↔thermal UDA | Closest masking precedent, but not VFM alignment; ALT-1′'s source has never seen NIR |
| AdaPGC extension, Nov 2, 2026 ([arXiv 2611.03232](https://arxiv.org/abs/2611.03232)); DASP CVPR'26; BriMPR AAAI'26 | Multimodal test-time adaptation under "modality reliability shifts", classification-centric | The reliability-shift framing is taken; ALT-1′ is dense and its sensor is unseen at training |
| FuseAdapt, NeurIPS'26 ([arXiv 2610.21779](https://arxiv.org/abs/2610.21779)) | Frozen VFM with per-modality LoRA; supervised; MCubeS RGB+AoLP+DoLP+NIR 57.62 | Needs labels; ALT-1′ is label-free |
| Pandey et al., ECCV'20 ([arXiv 2006.08696](https://arxiv.org/abs/2006.08696)); Texture-Shape Bias Balancing, ECML-PKDD'26 ([arXiv 2606.15072](https://arxiv.org/abs/2606.15072)) | Domain adaptation for NIR segmentation | Not a VFM plug-in; the red-channel→NIR gap they report is ALT-1′'s E0 question |

The safe and unsafe claims follow from that table.

**Safe**, each with "to our knowledge" and the citations above:

1. The first label-free method that adds a real NIR band to an already-trained, frozen RGB foundation segmenter and is evaluated on real adverse-weather RGB–NIR driving data.
2. Reliability-masked cross-spectral feature distillation, with evidence that the student exceeds its teacher in degraded regions. Cite ICRA'25 as the masking precedent.
3. The first zero-shot / open-vocabulary segmentation numbers on IDD-AW and from NIR. Call the Cityscapes-seen classes "zero-shot transfer"; reserve "open vocabulary" for the Cityscapes-unseen IDD classes, such as autorickshaw, billboard, curb and guard rail.
4. A controlled measured-band vs predicted-depth comparison inside an OV-DGSS model.
5. A Cityscapes→IDD-AW SOTA, **only** if the protocol-matched number exceeds 69.8.

**Must not claim**:

- First cross-modal distillation to an unlabeled modality (Gupta'16, ImageBind, OpenESS, OVOSE).
- First NIR alignment to a foundation model (SpectraDINO).
- First open-vocabulary segmentation with an auxiliary sensor under weather (MM-OVSeg).
- First OV-DGSS (Vireo).
- First multimodal, cross-modal or reliability-aware test-time adaptation (Night-TTA, MM-TTA, AdaPGC, DASP, BriMPR).
- First NIR domain adaptation (Pandey; ECML-PKDD'26).
- First semi-supervised multimodal segmentation (M3L [arXiv 2304.10756](https://arxiv.org/abs/2304.10756); SpiderMesh [arXiv 2303.08692](https://arxiv.org/abs/2303.08692)).
- First RGB–NIR fusion on IDD-AW. The WACV'24 paper, the ICPR'24 entries, UGF-Net and ICNTE 2026 precede it, and the benchmark claim belongs to A′ anyway.
- That NIR "penetrates" fog.
- Unqualified "open vocabulary" on IDD-AW's mostly familiar 26 classes.
- Any SOTA under a mismatched class set or split.

One more caution: a September 2026 paper found that forward-facing NIR "adds little to colour" once spatial leakage is removed ([arXiv 2609.13265](https://arxiv.org/abs/2609.13265)). Every split must stay sequence-disjoint, and the paper should say so explicitly.

### Data, splits and code: three alignment regimes on one fixed split

**Split.** Use the ICPR public split for both NIR papers: **3,430 train / 475 val / 1,095 test**, with test labels withheld. Val per condition is 120 rain, 154 fog, 100 low-light and 101 snow ([ICPR'24 report](https://arxiv.org/html/2409.05327v1)). The Paper B blueprint preferred the IDDAW_kit's older 3,926/1,072 split. Use that split only as a secondary report, and only if the download actually contains its test labels. Both papers must use the same split. Hold out a sequence-disjoint 10% of train as "dev" for every threshold and hyperparameter.

**IDD-AW facts.** 5,000 pixel-aligned pairs from a JAI FS-3200D prism camera: VIS 400–670 nm, NIR 740–1000 nm. Night NIR is probably passive (unverified) ([IDD-AW](https://arxiv.org/abs/2311.14459)).

**IDD-AW preparation (shared with A′).** The official kit's `createLabels_iddaw.py` is **missing from the repository**. Write your own converter, modelled on AutoNUE's `json2labelImg.py` and `helpers/anue_labels.py` ([IDDAW_kit](https://github.com/Furqan7007/IDDAW_kit); [AutoNUE public-code](https://github.com/AutoNUE/public-code)):

1. Rasterise the `gtSeg/<seq>/<frame>_mask.json` polygons in file order, with later objects overwriting earlier ones.
2. Skip `deleted` objects, strip `_new` suffixes and map `*group` labels.
3. Write `level3Ids` at native resolution for evaluation, plus a nearest-neighbour 1024×768 copy for training.
4. Store RGB and NIR as lossless 1024×768 PNG.
5. Patch `safe_iou.py` into an evaluator that accumulates one 26×26 confusion matrix per image. The released script has a hard-coded path filter and a "main classes" line that averages the wrong eight classes ([Paper A blueprint](../paper2_Q1_physics_when_NIR_helps/notes/paperA_experiment_blueprint.md)).

**19-class mapping for protocol 1 (ALT-1′ only).** By my reading of the two label lists, 17 Cityscapes classes map directly onto IDD-AW level-3. Cityscapes "terrain" and "train" have no IDD-AW counterpart, and the IDD-specific classes (autorickshaw, the fallback classes, curb, guard rail, billboard, bridge) must be ignored or mapped. SegBridge's mapping is unknown, so obtain it from the authors. Until then, report both a "17 shared classes" number and a number under SegBridge's protocol.

**The three regimes.** These keep the comparisons honest:

| Regime | Unlabeled pairs used for alignment | Fair comparators | Mandatory control |
|---|---|---|---|
| **R-DG** (domain generalisation) | FANVID train (4,502 pixel-aligned sets: JAI FS-3200T, VIS + NIR1 700–800 nm + NIR2 820–1000 nm; sunny, cloudy, rainy and snowy; no night) ([FANVID, ECCV'24](https://www.ecva.net/papers/eccv_2024/papers_ECCV/html/3338_ECCV_2024_paper.php)), plus MS2 only if verified pixel-aligned. **No IDD-AW images** | Vireo-style OV-DGSS; DG source-only | — |
| **R-UDA** (unlabeled target) | IDD-AW train images (3,430, labels unused) + FANVID | SegBridge, TENT, CoTTA | **RGB-only self-training on the same 3,430 images** (target-exposure control) |
| **R-TTA** (online) | Test stream only | SegBridge (continual), TENT, CoTTA | The same TTA on the RGB-only stream, over 3 condition orders |

**FANVID band matching.** Approximate IDD-AW's 740–1000 nm band by averaging FANVID's NIR1 and NIR2 ([Paper A blueprint](../paper2_Q1_physics_when_NIR_helps/notes/paperA_experiment_blueprint.md)). FANVID's segmentation masks are only Mask2Former pseudo-labels, which is irrelevant here because alignment uses no labels ([dataset audit](../common/background_notes/wave1_datasets_audit.md)).

**Other datasets.** MCubeS (500 sets, 302/96/102, 20 material classes, NIR as a warped channel with a validity mask) loads through the StitchFusion loader ([StitchFusion repo](https://github.com/LiBingyu01/StitchFusion)). Cityscapes and ACDC serve as source and as Vireo-reproduction sanity data.

**Code.**

| Component | Source | Status |
|---|---|---|
| Backbone and decoder | DINOv2 ([facebookresearch/dinov2](https://github.com/facebookresearch/dinov2)) + Mask2Former ([facebookresearch/Mask2Former](https://github.com/facebookresearch/Mask2Former)) | Repositories verified to exist |
| Supervised RGB-X baselines | StitchFusion, DELIVER (CMX/CMNeXt) ([jamycheung/DELIVER](https://github.com/jamycheung/DELIVER)), mmsegmentation SegFormer | Repositories verified to exist |
| LoRA | Any PEFT implementation | Not researched; choose one |
| Vireo, SegBridge, SpectraDINO, CAT-Seg, FC-CLIP, SED, Talk2DINO, TENT, CoTTA | — | **Not researched or not accessible; verify** (see the verification table below) |

### The method, specified to the level of code

**Frozen part.** The frozen part is the source segmenter, S = D∘E_R. For protocols 1 and 3 it is a DINOv2-B/14 encoder E_R and a Mask2Former decoder D trained on Cityscapes' 19 classes; for protocol 2 it is the visual encoder of an OV-DGSS model (Vireo, or CAT-Seg/FC-CLIP if Vireo cannot run in 24 GB). If no public Cityscapes DINOv2 + Mask2Former checkpoint exists, train one with a frozen backbone and lightweight adapters. That is the SegBridge-style design, which reached its best results with LoRA rank 8. If the source model carries backbone adapters, "E_R" means the adapted backbone. Nothing in S is ever updated.

**Student.** The NIR student E_N starts as an exact copy of E_R. Only two things are trainable: LoRA adapters (rank 16, α 32) on the attention projections of all 12 blocks, and a patch embedding initialised from the RGB one and fed the NIR band replicated to three channels. The notes confirm that DINOv2-B with LoRA rank 8–16 fits 24 GB, and that ViT-L needs batch 2–4 with gradient checkpointing ([alternatives audit](notes/alternative_astar_ideas.md)).

**Alignment loss.** For an unlabeled pixel-aligned pair (x_R, x_N), with the same crop and flip applied to both:

L = Σ_l Σ_p m(p)·[1 − cos(E_N^l(x_N)_p, E_R^l(x_R)_p)] / Σ_p m(p) + λ_cls·[1 − cos(cls_N, cls_R)] + λ_out·Σ_p m(p)·KL( softmax(D(E_R)_p / T) ‖ softmax(D(E_N)_p / T) )

Here l runs over the layers the decoder consumes (for a 12-block ViT-B with four taps, blocks 3, 6, 9 and 12), and the output term uses Mask2Former's per-pixel semantic distribution. Starting values, to sweep on dev, are λ_cls = 0.1, λ_out = 1 and T = 2. The SpectraDINO loss set (CLS cosine, symmetric InfoNCE, patch alignment, neighbourhood KL) is reimplemented as the closest-method baseline, not used in the main method.

**Reliability mask m(p).** Five variants, all computed from the frozen RGB stream at patch resolution:

| Mask | Definition | Role |
|---|---|---|
| M0 | m ≡ 1 | Unmasked |
| M1 | 1 for images from FANVID's sunny and cloudy subsets, 0 otherwise | Clear-image mask |
| M2 | 1[max-class probability of S(x_R) ≥ 0.9], or the soft version 1 − H/log C | Teacher confidence or entropy; **the default** |
| M3 | 1[RGB luminance log-CNR ≥ κ], using A′'s Poisson–Gaussian noise code | RGB-SNR mask; ablation only |
| M4 | M2 × M3 | Combination |

**Training recipe** (my defaults, to be measured in week 1):

| Setting | Value |
|---|---|
| Optimiser | AdamW; LoRA lr 1e-4, patch-embed lr 1e-5; weight decay 0.01 |
| Schedule | 20k iterations |
| Batch | 8 pairs |
| Crops | 518×518 random crops from 1024×768 images |
| Precision | fp16 with gradient checkpointing; teacher forward without gradients |

**Test-time fusion.** For each tapped layer and patch, fuse the two streams' features:

f = w ⊙ E_R(x_R) + (1 − w) ⊙ E_N(x_N)

then pass the fused features to the frozen D. The weight w comes from a training-free rule:

| Rule | Weight |
|---|---|
| F0 | RGB only (w = 1) |
| F1 | NIR only (w = 0) |
| F2 | Mean (w = 0.5) |
| F3 | **Entropy:** w = e^(−H_R/τ) / (e^(−H_R/τ) + e^(−H_N/τ)), where H is the entropy of each stream's own prediction |
| F4 | **Agreement:** mean where cos(E_R, E_N) ≥ δ, entropy rule elsewhere |
| F5 | Oracle per-pixel choice using labels (upper bound only) |

Cost: two backbone passes and three decoder passes. Report the latency.

**Optional TTA stage (the ALT-2 idea).** Updates only the student's LoRA online. Loss: cross-entropy of the NIR prediction against RGB pseudo-labels, on pixels where RGB confidence is ≥ 0.9 and the two streams agree, plus entropy minimisation of the fused output. Use an EMA teacher with momentum 0.999. Run 3 condition orders, because continual-TTA results depend on stream order (my inference).

**Teacher-degradation analysis (the core figure).** Bin val pixels by teacher reliability (RGB entropy deciles and, separately, RGB-SNR deciles) and plot three curves: teacher mIoU, the unmasked student's NIR-only mIoU, and the masked student's NIR-only mIoU. The claim holds only if the masked student overtakes the teacher in the low-reliability bins while the unmasked one does not.

### Baselines and the exact numbers to beat

Expected condition ordering. The alternatives audit expected the largest gains "in low light and fog". The physics and supervised evidence point elsewhere: fog is large-droplet, wavelength-flat Mie scattering, and supervised NIR-only is 6 points *worse* than RGB in fog (58 vs 64) ([IDD-AW](https://arxiv.org/html/2311.14459)). Pre-register **rain ≥ low light > fog ≈ snow**, and treat any fog gain as a finding, not a promise.

| Protocol | Classes / split | Published numbers to beat or anchor to | My target [heuristic] |
|---|---|---|---|
| **P1 Cityscapes → IDD-AW** | 19 Cityscapes classes, IDD-AW val | SegBridge **69.8** (text) or **65.1** (own ablation); its IMP baseline **58.4**; ACDC context ViT-S 65.8, ViT-B 69.5, ViT-L 74.2 ([supplement](https://openaccess.thecvf.com/content/CVPR2026F/supplemental/Jangamreddy_Continual_Adaptation_of_CVPRF_2026_supplemental.pdf)). Cross-dataset anchor: Cityscapes-trained InternImage-B **46** on IDD-AW ([dataset audit](../common/background_notes/wave1_datasets_audit.md)) | **≥ 70.0** with NIR; ≥ +1.5 over the identical RGB-only pipeline; ≥ +1.0 over the target-exposure control |
| **P2 OV-DGSS** | Sanity: Cityscapes → ACDC. New: IDD-AW, 26 classes, zero-shot | Vireo vs best open-vocabulary baseline on ACDC: night **60.6** vs FC-CLIP 40.8; fog **82.3** vs SED 69.0; rain **76.3** vs FC-CLIP 63.2; snow **76.2** vs FC-CLIP 61.5 ([Vireo](https://arxiv.org/html/2506.09881)). No published IDD-AW numbers, so our RGB-only reproductions are the baselines | Reproduce Vireo on ACDC within about 2 points under 24 GB; then the NIR plug-in gives ≥ +1.5 mIoU on IDD-AW for ≥ 2 of 3 OV models |
| **P3 supervised IDD-AW** | 26 classes, val 475 | SixthSense Mask2Former Swin-L RGB **69.51 val**. Leaderboard: **68.54** (RGB) and **68.32 / 64.73 SmIoU** (KweenCoders RGB+NIR). Organiser RGB+NIR 67.30 ([ICPR'24](https://arxiv.org/html/2409.05327v1)) | ≥ 70.0 val with the DINOv2-B host (the blueprint's VFM target) |
| **P4 MCubeS RGB-N** | 20 material classes | SpectraDINO **46.76** (ViT-L); StitchFusion **51.67 / 53.21 / 54.59** (MiT-B4 / +FFMs / Swin-L) ([StitchFusion](https://arxiv.org/html/2408.01343)) | Beat SpectraDINO at ViT-B with the aligned student as initialisation; claim only "competitive" with 53.21 |
| **P5 label-efficient** | IDD-AW, 1/16, 1/8 and 1/4 of train, sampled by sequence | None published | ≥ +2 mIoU at 1/16 over RGB-only and 4-channel stacking |

**Open-vocabulary prompts.** IDD-AW's class names need fixed text prompts written before evaluation; "obs-str-bar-fallback" and "vehicle fallback", for example, are not natural-language concepts. Report seen and unseen classes (with respect to Cityscapes) separately.

### Every ablation row, and the statistics that decide them

| Row | Setting | Question it answers | Seeds |
|---|---|---|---|
| A0 | RGB source-only | Lower bound | deterministic |
| A1 | Raw NIR replicated ×3 into the frozen S | How far raw NIR is from RGB features (E0) | det. |
| A2 | NIR placed in the red channel | Pandey-style "near-red" bridge | det. |
| A3 | HAQAGen NIR→RGB, then S | "Why not translate?" control; uses the group's colorizer as a baseline, not the method | det. |
| A4 | SpectraDINO-recipe student, same fusion | Closest method | 3 |
| A5 | Unmasked patch-cosine distillation (M0) | Naive alignment | 3 |
| A6 | A5 + output KL | Value of output distillation | 1 |
| A7 | M1 clear-image mask | Coarse reliability | 1 |
| **A8** | **M2 confidence/entropy mask** | **Core claim** | 3 |
| A9 | M3 RGB-SNR mask | Physics mask (ablation only; A′ owns physics) | 1 |
| A10 | M4 = M2 × M3 | Combination | 1 |
| A11 | Random mask matched in coverage to A8 | Masking vs simply using less data | 1 |
| A12 | FANVID only (R-DG) / IDD-AW only / both (R-UDA) | Where the gain comes from | 1 each |
| A13 | LoRA rank 4 / 8 / 16 / 32; last-4 vs all taps | Capacity | 1 |
| A14 | Fusion rules F0–F5 | Test-time fusion | eval-only |
| A15 | Feature-level vs logit-level fusion | Where to fuse | eval-only |
| A16 | TTA stage vs TENT and CoTTA on the RGB-only stream | TTA value and order sensitivity | 3 orders |
| **A17** | **RGB-only self-training on the same unlabeled IDD-AW images** | **Target-exposure control** | 3 |
| A18 | Vireo with predicted depth / with NIR / with both | Measured band vs predicted geometry | 1 |
| A19 | ViT-B vs ViT-L | Scale | 1 |
| A20 | Label fraction 1/16, 1/8, 1/4, 1: RGB-only vs 4-channel stacking vs plug-in initialisation | Label efficiency (ALT-3 folded in) | 1 (3 at 1/16) |
| A21 | RGB–NIR disagreement as an error detector: AUROC, and Safe mIoU under abstention | Failure detection (ALT-4a folded in) | eval-only |

**Statistical protocol** (mirroring the Paper A blueprint):

1. **Seeds.** 3 seeds (3407/3408/3409) for the rows marked 3. Report mean ± SD.
2. **Bootstrap.** Paired cluster bootstrap that resamples **val sequences** with replacement, B = 10,000. Each replicate recomputes mIoU from summed per-image confusion matrices for both arms on the same resample.
3. **Pre-registration.** Commit the family of tests in week 5, before the confirmatory runs:
   - full method vs A0 (P1);
   - full method vs A17 (P1);
   - A8 vs A5, overall and on low light;
   - A8 vs A4;
   - the NIR plug-in vs no plug-in, for Vireo and for CAT-Seg (P2);
   - P3 full method vs DINOv2 RGB-only;
   - A20 at 1/16.

   That is nine tests, Holm-corrected at α = 0.05; everything else is labelled exploratory.
4. **Detectable effect.** Estimate the minimum detectable effect in week 2 from A0's bootstrap SE. Per-condition CIs on about 100 images will be wide, so per-condition claims should rest on 3-seed consistency plus the all-val test.

This also meets NeurIPS's error-bar checklist.

### Figures and tables that make the argument

| Item | What it shows |
|---|---|
| Fig. 1 (teaser) | A frozen RGB foundation segmenter with a NIR plug-in. Low-light and rain frames: RGB, NIR, RGB-only prediction, +NIR prediction. Inset: per-condition mIoU gain bars |
| Fig. 2 (method) | (a) Masked alignment on unlabeled pairs, with the mask visualised; (b) test-time per-pixel fusion; (c) the optional TTA loop |
| Fig. 3 (core evidence) | The teacher-degradation curves described above: teacher, unmasked and masked student mIoU by reliability decile |
| Fig. 4 | Fusion-weight maps w per condition and their histograms. Shows where the model trusts NIR, and answers "reliability is just attention" critiques |
| Fig. 5 | Qualitative grid: 4 conditions × [RGB, NIR, GT, source, best RGB TTA, ours, w map, error map] |
| Fig. 6 | Label-efficiency curves (A20) |
| Fig. 7 | OV-DGSS results per condition on IDD-AW, plus the depth vs NIR swap (A18) |
| Fig. 8 (supplement) | Failure cases, and error-detection ROC curves (A21) |
| Tab. 1 | P1, Cityscapes → IDD-AW, per condition: source-only, TENT, CoTTA, SegBridge (reported), A17 target-exposure control, ours RGB-only, ours +NIR (R-DG and R-UDA rows) |
| Tab. 2 | P2: Vireo, CAT-Seg, FC-CLIP and Talk2DINO with and without the plug-in on ACDC (sanity) and IDD-AW, seen vs unseen classes |
| Tab. 3 | P3 supervised IDD-AW plus a "reported, different protocol" block (ICPR, InternImage) |
| Tab. 4 | Main ablation: A4–A13, A17 |
| Tab. 5 | P4 MCubeS RGB-N vs SpectraDINO and StitchFusion |
| Tab. 6 | Efficiency: parameters, trainable parameters, latency at 1024×768, memory |
| Tab. 7 | A3 colorize-then-segment and A18 depth vs NIR controls |

### Compute: about 650–800 GPU-hours, with cuts identified

All figures are my estimates, to be replaced by week-1 measurements. The notes give only "about 1–2 days per run" for a DINOv2-B LoRA student, and roughly 45 GB peak memory for Vireo at batch 8.

| Block | Runs | GPU-h |
|---|---|---|
| Cityscapes source model (only if no public checkpoint) | 1–2 | ~40–60 |
| E0 inference (A0–A3) | — | ~5 |
| Alignment screening (1 seed, 10k iterations; A5–A13) | ~14 | ~110 |
| Key alignments (A4, A5, A8, R-DG best; 3 seeds at 20k iterations) | 12 | ~150–290 |
| Fusion and TTA evaluation, 3 stream orders | — | ~20 |
| Target-exposure control and TENT/CoTTA (A16–A17) | 4–6 | ~50 |
| OV-DGSS: Vireo reproduction cut to 24 GB, + NIR variants, other OV inference | 3–4 trainings | ~100–120 |
| Supervised and label-efficient runs (P3, A20) | ~12 | ~100–130 |
| MCubeS RGB-N | 4 | ~40 |
| ViT-L check (A19) | 1 | ~40–70 |
| **Total** | | **~650–800** |

The first cuts if time runs short are A19 (the ViT-L run) and the MCubeS row, which together save about 80–110 GPU-hours.

### Details never researched for ALT-1′: verify before relying on them

| Item | Status in the notes | Action |
|---|---|---|
| SegBridge: which number is the reference (69.8 or 65.1); backbone; 19-class mapping; val split; code | Main paper and code not accessed (HTTP 403) | Email the authors in week 1; until they answer, compare against our own reproduction |
| Vireo code and weights; 24 GB feasibility (paper peak ~45 GB at batch 8); target class sets | Code availability not checked | Verify; fall back to CAT-Seg / FC-CLIP if it cannot fit |
| SpectraDINO code and weights; its MCubeS protocol (frozen or fine-tuned) | Not checked | Verify; otherwise reimplement its loss set |
| MS2 RGB–NIR pixel alignment and NIR illumination | From memory; may be rectified stereo only | Verify; if not aligned, use only CLS-level losses on MS2 |
| A public Cityscapes-trained DINOv2 + Mask2Former checkpoint | Not researched | Search; budget 40–60 GPU-h to train one |
| CAT-Seg, FC-CLIP, SED, Talk2DINO, dino.txt repositories and weights | Not researched | Verify each before week 8 |
| TENT and CoTTA segmentation implementations | Not researched | Verify |
| HAQAGen inference code at IDD-AW resolution | Group-internal ([project page](https://rajeev-dw9.github.io/HAQAGen)) | Confirm with co-authors |
| RANUS availability | Login-gated | Optional |
| FANVID licence | None stated | Ask the authors before releasing derived models |
| Non-arXiv scoop check (T-ITS, TIV, RA-L) for "NIR + VFM alignment" | Not done | Run in weeks 1 and 20 |
| Real speed and memory on the student's GPU | Unknown | Measure in week 1 |

