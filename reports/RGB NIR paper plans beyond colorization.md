# Plug NIR In, Then Prove When It Helps

The research notes support the coordinator's portfolio. The **A\* paper should be ALT-1′**: a label-free NIR plug-in for a frozen RGB foundation segmenter. A NIR LoRA student is aligned to frozen RGB features on unlabeled RGB–NIR pairs, distilling only where the RGB teacher is reliable; RGB and NIR are then fused per pixel at test time, with optional cross-spectral test-time adaptation. It should target **NeurIPS 2027 (about early May 2027, estimated)**, because it is the only candidate with a public-benchmark route to better-than-published numbers. The route is Cityscapes→IDD-AW, where the best published RGB-only result is SegBridge's **69.8 mIoU** ([SegBridge supplement](https://openaccess.thecvf.com/content/CVPR2026F/supplemental/Jangamreddy_Continual_Adaptation_of_CVPRF_2026_supplemental.pdf)). The **Q1 paper should be Paper A′**: a falsifiable spectral-physics account of when NIR helps RGB–NIR segmentation. It measures NIR's value with cross-fitted per-pixel counterfactual utility, tests gates on held-out conditions and a second camera, and builds an IDD-AW-C corruption suite with matched control pairs. Paper C's per-class spectral complementarity is folded in as A′'s material factor. A′ targets IEEE TIP or Information Fusion around August 2027. Honestly scored, ALT-1′ is **6/10 for A\*** (about 7 if its headline numbers hold) and **8/10 for Q1**, while A′ is **6/10 for A\*** (7 with a clean decisive experiment, 4 if its effects sit inside seed noise) and **7.5/10 for Q1**; no candidate in the notes is verifiably at 7 or above on concept alone. Neither paper ever turns NIR into RGB, and the group's HAQAGen-style colorization appears only as a control baseline. This audit adds three amendments. First, ALT-1′ must separate a domain-generalisation regime from an unlabeled-target regime and beat an RGB-only "target exposure" control, because SegBridge's 69.8 is itself uncertain: its own ablation table says 65.1, it scores 19 Cityscapes classes, and its backbone is unreported. Second, the cross-fitted, seed-null utility estimator already underpins the thesis's Paper 2, so A′ must present it as a measuring instrument and ALT-1′ must not use it. Third, added to thesis Papers 1–2 (due March and about April 1, 2027), this is roughly five papers on one 24 GB GPU, so something has to give. No plan can guarantee better results; what this one does is place seven go/no-go gates in the first eleven weeks, each costing at most a few hundred GPU-hours, with a publishable fallback behind every failure.

## The coordinator's portfolio holds, with three amendments

Every score the coordinator relied on is in the notes:

| Candidate | A\* | Q1 | Source |
|---|---|---|---|
| ALT-1′ | 6, ceiling 7 | 8 | [alternatives audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/alternative_astar_ideas.md>) |
| Paper A′ (reframe) | 6 (7 if the decisive experiment is clean, 4 if it is not) | 7.5 | [reframe audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_reframe_physics_ood.md>) |
| Paper B | 3–4 | 6 (5 without light-source stratification) | [Paper B audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperB_novelty_audit.md>) |
| Paper C | 4 | 6.5 | [venues and Paper C note](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/venues_figures_and_paperC.md>) |

The original Paper A, a method paper on counterfactually supervised physics gates, fell to **5 A\* / 7 Q1** because three mid-2026 preprints pre-empted it. **TIER-MoE** already trains routing on cross-fitted, out-of-fold modality-risk targets ([arXiv 2607.27289](https://arxiv.org/abs/2607.27289)); **Boundary-Aligned Contribution Routing** already defines leave-one-modality-out utility, a negative-transfer rate and gate–utility Spearman correlations ([arXiv 2608.15261](https://arxiv.org/abs/2608.15261)); and **GAUGE** already gates on counterfactual effects ([arXiv 2608.05608](https://arxiv.org/abs/2608.05608)). So the coordinator's reading is accurate: the A\* slot is a tie at 6 between ALT-1′ and A′, and the tie has to be broken by something other than concept novelty.

The tie-breaker is the *kind* of evidence each paper can produce. ALT-1′ adds a second real measurement to a setting where RGB-only SOTA numbers are already public. SegBridge reports 58.4 → 69.8 mIoU on IDD-AW val from Cityscapes. Vireo's open-vocabulary domain-generalisation protocol has no NIR or IDD target at all ([Vireo](https://arxiv.org/html/2506.09881)). SpectraDINO's NIR alignment of DINOv2 reaches only **46.76 mIoU on MCubeS RGB+NIR**, far below supervised fusion, which signals headroom for a better alignment ([SpectraDINO](https://arxiv.org/html/2605.02258)).

A′, by contrast, studies effects that are small on real data. On IDD-AW, adding NIR to RGB gains **+4, +1, +1 and 0 mIoU** in rain, fog, low light and snow ([IDD-AW](https://arxiv.org/html/2311.14459)). Differences between gate arms on a held-out condition may therefore sit inside seed noise, which is why the reframe audit rests A′'s headline on dense metrics rather than mIoU. A\* reviewers reward numbers on public benchmarks, while journals reward careful falsification and tolerate honest nulls. The rational allocation puts the high-variance, number-driven paper in the deadline-bound A\* slot. The paper whose value survives either outcome goes into the journal slot, which has no deadline.

Folding Paper C into A′ also checks out. C alone is a 6.5 Q1 paper whose central test is under-powered: with only **19–26 classes**, a two-sided Spearman test at α = 0.05 needs ρ of roughly 0.45 (n = 20) to 0.39 (n = 26). Its "physics prior plus conformal sets" structure also invites the "two loosely coupled halves" critique ([venues note](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/venues_figures_and_paperC.md>)). Keeping only its per-class spectral complementarity, as A′'s fourth physical factor, and dropping the conformal half removes both problems.

Demoting Paper B is also justified, on three counts. It **reuses the thesis**: it takes Paper 1's P0–P4 protocols and regret-rate metric, and revives the "no-regret enhancement" idea the thesis roadmap had already retired at 3/10 ([roadmap](</home/user/lowlight/research/PUBLICATION_ROADMAP.md>)). Its **no-regret design has a logical hole**: with the gate at zero it returns the RGB-only enhancer's output, not the raw input ([Paper B audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperB_novelty_audit.md>)). And the **passive-NIR evidence is thin**: in the ICPR 2024 competition, an RGB-only Mask2Former (68.54) beat DarkVisionNet-preprocessed RGB+NIR (67.58) ([ICPR'24 report](https://arxiv.org/abs/2409.05327)).

### Amendment 1: ALT-1′'s comparisons are only fair if the regimes are separated

A NIR student aligned on *unlabeled IDD-AW training images* has seen the target domain. That makes it an unsupervised-domain-adaptation result, not a domain-generalisation one. A comparison with Vireo (pure DG) is only fair for a student aligned on non-IDD pairs, such as FANVID. A comparison with SegBridge (continual test-time adaptation) is fair for the unlabeled-target regime, but it then needs an RGB-only control that sees the same unlabeled IDD-AW images; otherwise the gain could come from target exposure rather than from NIR. The notes do not raise this; it is my inference from how the protocols are defined, and it is the first thing a NeurIPS reviewer would check.

The headline number is also softer than it looks. SegBridge's text says **69.8**, but its own ablation lists "All components (ours)" at **65.1**. It scores **19 Cityscapes classes**, not IDD-AW's 26, and the backbone behind 69.8 is unknown: its ACDC table spans ViT-S 65.8 to ViT-L 74.2 ([SegBridge supplement](https://openaccess.thecvf.com/content/CVPR2026F/supplemental/Jangamreddy_Continual_Adaptation_of_CVPRF_2026_supplemental.pdf)). Email the authors in week 1.

### Amendment 2: each shared tool belongs to exactly one paper

The thesis's Paper 2 is built on a *K-fold cross-fitted, raw-vs-raw seed-noise-calibrated per-object treatment effect* ([roadmap](</home/user/lowlight/research/PUBLICATION_ROADMAP.md>)). A′'s utility, u = CE(RGB, ∅) − CE(RGB, NIR), uses the same estimator family: computed out of fold and standardised by a seed-disagreement null ([Paper A blueprint](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_experiment_blueprint.md>)). If both papers claim this estimator as novel, a reviewer who sees both will call it salami-slicing. The fix is for A′ to cite thesis Paper 2 and present dense utility as its *measurement instrument*, not its contribution, which is how the reframe audit already frames it. A′ should also call its pixel-level failure metric a null-calibrated **negative-transfer rate (NTR)**, citing both 2608.15261 and thesis Paper 1, rather than a new "regret rate". ALT-1′ also distils only where a signal is trustworthy, which echoes thesis Paper 2's gated distillation, so it must use *label-free teacher reliability* (entropy, confidence, agreement) as its mask and never cross-fitted utility.

The same ownership rule separates the two NIR papers. A′ owns the physics descriptors and the physics account, the controlled RGB-X benchmark on IDD-AW, and IDD-AW-C. ALT-1′ uses physics descriptors only as one ablation row of its mask, reports only two or three supervised baselines, and does not evaluate on IDD-AW-C in the main paper. This keeps A′'s first-benchmark and first-suite claims intact, even though ALT-1′ is submitted first.

### Amendment 3: five papers on one GPU needs a decision now

The thesis roadmap already schedules Paper 1 for a Q1 journal in March 2027 and Paper 2 for ACM MM around April 1, at about **550 GPU-hours** for Paper 2 alone, plus a conditional Paper 3 ([roadmap](</home/user/lowlight/research/PUBLICATION_ROADMAP.md>)). The NIR papers add roughly 650–800 GPU-hours (ALT-1′) and 480–560 (A′); both are my estimates, detailed below.

The Paper A blueprint counts about **1,870 GPU-hours available between Nov 3 and Mar 15 at 60% utilisation** ([blueprint](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_experiment_blueprint.md>)). GPU time is therefore tight but workable if A′'s heavy runs slip to April–June. The student's hours are the real constraint, so the plan below assumes that thesis Paper 3 is deferred, that a co-author (for example a HAQAGen co-author) takes the light-source annotation and the IDD-AW-C build, and that the advisor accepts A′ being submitted after the A\* deadline.

### The two papers share plumbing but make different claims

| Dimension | ALT-1′ (A\*) | A′ (Q1) | Thesis (Papers 1–3) |
|---|---|---|---|
| Question | Can a real NIR band be added to a frozen RGB foundation segmenter *without NIR labels*, and does it beat RGB-only SOTA? | *Where and why* does NIR change a trained segmenter's loss, and which gate design survives unseen conditions and cameras? | Does low-light enhancement help detection, and can enhancer privilege be distilled? |
| Supervision | Label-free alignment; Cityscapes labels only | Fully supervised IDD-AW hosts | Supervised detection |
| Core evidence | mIoU vs SegBridge, Vireo and supervised SOTA | Gate–utility Spearman, sign-AUROC, null-calibrated pixel NTR on held-out conditions; physics predictions | mAP with paired bootstrap |
| Owns | Masked cross-spectral distillation; NIR plug-in; first zero-shot numbers on IDD-AW | Physics account; IDD-AW RGB-X benchmark; IDD-AW-C; light-source labels | Protocols P0–P4; regret rate; cross-fitted seed-null estimator |
| Uses, without claiming | RGB-SNR mask (ablation row only) | Cross-fitted utility (cites thesis Paper 2) | — |
| Data | IDD-AW, FANVID, Cityscapes, ACDC, MCubeS | IDD-AW, IDD-AW-C, MCubeS, Hyperspectral City V2 | ExDark, DarkFace, NOD |
| Distance from colorization | No image synthesis; HAQAGen only as a "colorize-then-segment" control | No image synthesis; IDD-AW-C degrades real bands; Hyperspectral City V2 is integrated through band curves | Not NIR |

## ALT-1′ scores a 6 that strong numbers could lift to 7

### Every ingredient has precedent, but the combination does not

The arXiv sweeps found **no paper doing open-vocabulary or zero-shot segmentation from NIR or RGB+NIR, and none evaluating open-vocabulary segmentation on IDD-AW**. Every ingredient, however, has a close neighbour ([alternatives audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/alternative_astar_ideas.md>)). The literal ALT-1, which aligns NIR to DINOv2 and then segments from it, scores only 5/10. The obvious attack is "SpectraDINO plus Talk2DINO, applied to NIR".

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
5. Patch `safe_iou.py` into an evaluator that accumulates one 26×26 confusion matrix per image. The released script has a hard-coded path filter and a "main classes" line that averages the wrong eight classes ([Paper A blueprint](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_experiment_blueprint.md>)).

**19-class mapping for protocol 1 (ALT-1′ only).** By my reading of the two label lists, 17 Cityscapes classes map directly onto IDD-AW level-3. Cityscapes "terrain" and "train" have no IDD-AW counterpart, and the IDD-specific classes (autorickshaw, the fallback classes, curb, guard rail, billboard, bridge) must be ignored or mapped. SegBridge's mapping is unknown, so obtain it from the authors. Until then, report both a "17 shared classes" number and a number under SegBridge's protocol.

**The three regimes.** These keep the comparisons honest:

| Regime | Unlabeled pairs used for alignment | Fair comparators | Mandatory control |
|---|---|---|---|
| **R-DG** (domain generalisation) | FANVID train (4,502 pixel-aligned sets: JAI FS-3200T, VIS + NIR1 700–800 nm + NIR2 820–1000 nm; sunny, cloudy, rainy and snowy; no night) ([FANVID, ECCV'24](https://www.ecva.net/papers/eccv_2024/papers_ECCV/html/3338_ECCV_2024_paper.php)), plus MS2 only if verified pixel-aligned. **No IDD-AW images** | Vireo-style OV-DGSS; DG source-only | — |
| **R-UDA** (unlabeled target) | IDD-AW train images (3,430, labels unused) + FANVID | SegBridge, TENT, CoTTA | **RGB-only self-training on the same 3,430 images** (target-exposure control) |
| **R-TTA** (online) | Test stream only | SegBridge (continual), TENT, CoTTA | The same TTA on the RGB-only stream, over 3 condition orders |

**FANVID band matching.** Approximate IDD-AW's 740–1000 nm band by averaging FANVID's NIR1 and NIR2 ([Paper A blueprint](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_experiment_blueprint.md>)). FANVID's segmentation masks are only Mask2Former pseudo-labels, which is irrelevant here because alignment uses no labels ([dataset audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/wave1_datasets_audit.md>)).

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

**Student.** The NIR student E_N starts as an exact copy of E_R. Only two things are trainable: LoRA adapters (rank 16, α 32) on the attention projections of all 12 blocks, and a patch embedding initialised from the RGB one and fed the NIR band replicated to three channels. The notes confirm that DINOv2-B with LoRA rank 8–16 fits 24 GB, and that ViT-L needs batch 2–4 with gradient checkpointing ([alternatives audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/alternative_astar_ideas.md>)).

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
| **P1 Cityscapes → IDD-AW** | 19 Cityscapes classes, IDD-AW val | SegBridge **69.8** (text) or **65.1** (own ablation); its IMP baseline **58.4**; ACDC context ViT-S 65.8, ViT-B 69.5, ViT-L 74.2 ([supplement](https://openaccess.thecvf.com/content/CVPR2026F/supplemental/Jangamreddy_Continual_Adaptation_of_CVPRF_2026_supplemental.pdf)). Cross-dataset anchor: Cityscapes-trained InternImage-B **46** on IDD-AW ([dataset audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/wave1_datasets_audit.md>)) | **≥ 70.0** with NIR; ≥ +1.5 over the identical RGB-only pipeline; ≥ +1.0 over the target-exposure control |
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

## Paper A′ earns its Q1 even if the physics fails

### The science claim is open, even though every method component has a neighbour

The reframe audit's verdict is that **unseen-condition fusion is not new**. Seeing Through Fog trained on clear weather only and tested on unseen adverse weather, using a hand-crafted entropy cue ([arXiv 1902.08913](https://arxiv.org/abs/1902.08913)), and DCDA (ECCV 2026) defined type-open and severity-open weather protocols for LiDAR–radar fusion ([arXiv 2607.01983](https://arxiv.org/abs/2607.01983)). What is still open is a quantitative, per-pixel *spectral-physics* model of a trained RGB–NIR segmenter's marginal NIR utility, checked against cross-fitted counterfactual utility, together with a head-to-head of physics-constrained vs learned vs condition-token gates on held-out conditions in RGB-X segmentation. A new close threat is RAF (ECCV 2026), which explicitly supervises per-pixel reliability maps using cross-modal-similarity pseudo-labels ([arXiv 2607.04587](https://arxiv.org/abs/2607.04587)).

Novelty therefore rises from 5 to **about 6/10 for A\*** and from 7 to **7.5/10 for a Q1 journal**. A′ must lead with the scientific claim and present gates as the instrument that tests it ([reframe audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_reframe_physics_ood.md>)). The recommended title is **"When Does Near-Infrared Help? A Falsifiable Spectral-Physics Account of Modality Utility for Robust RGB–NIR Segmentation."** Avoid "Learning Where to Trust…", which collides with arXiv 2610.36672.

| A′ factor or component | Closest prior work | What remains open |
|---|---|---|
| F1 scattering regime: haze ∝ λ^−q vs Mie fog q ≈ 0 | Kim visibility model; HSVF (single β); Bishoff 2023 (heuristically reduced NIR fog, 40% dimmer NIR snow) ([arXiv 2305.11347](https://arxiv.org/abs/2305.11347)) | A per-pixel implied-q̂ descriptor in a segmentation gate; "utility ≈ 0 at q = 0" tested on a matched spectral-vs-flat pair |
| F2 illuminant spectrum | Elvidge 2010: HPS and metal halide emit strongly at 819 nm, incandescent peaks at 900–1,050 nm, and white LEDs have "virtually no emission beyond 800 nm" ([Sensors](https://pmc.ncbi.nlm.nih.gov/articles/PMC3274255/)). Lamp classification exists only in remote sensing | **Not found in perception fusion.** Must show it adds beyond ΔCNR |
| F3 per-band photon budget (Poisson–Gaussian σ, CNR) | Foi 2008 estimator; CSNR band quality ([arXiv 2508.10962](https://arxiv.org/abs/2508.10962)); Fourier descriptors in SRD ([arXiv 2606.01173](https://arxiv.org/abs/2606.01173)); entropy in Seeing Through Fog | σ and ΔCNR as *monotone* gate inputs that extrapolate to held-out low light |
| F4 material contrast (from Paper C) | NDVI/EVI channels in AdapNet/CMoDE (2016–17); MeCSAFNet ([arXiv 2602.10137](https://arxiv.org/abs/2602.10137)); band selection on Hyperspectral City (Li et al., OJITS 2026; WHISPERS 2026 [arXiv 2609.31074](https://arxiv.org/abs/2609.31074)); PID for MRI input selection ([arXiv 2607.15396](https://arxiv.org/abs/2607.15396)) | A library- and camera-curve-derived per-class VIS-vs-NIR prior that *predicts* NIR utility |
| F5 dense counterfactual utility as target and falsifier | TIER-MoE, 2608.15261, GAUGE (sample-level); RAF (per-pixel, similarity pseudo-labels); RSFNet (saliency) | A dense *counterfactual-utility* target for an RGB-X segmentation gate, also used as a falsification instrument |
| F6 held-out-condition (LOCO) and cross-camera generalisation | Seeing Through Fog; DCDA; 3D-LRF and CAFuser (categorical condition gates; CAFuser has no held-out metric) ([arXiv 2410.10791](https://arxiv.org/abs/2410.10791)) | **Open for RGB-X segmentation** |
| F7 IDD-AW-C with matched controls | Bishoff (overhead, heuristic); SynFog and FogDrive (visible-only physics) ([arXiv 2607.22698](https://arxiv.org/abs/2607.22698)); sensor-failure benchmark ([arXiv 2503.18445](https://arxiv.org/abs/2503.18445)) | A physically parameterised per-band driving suite with spectral/flat and LED/halogen matched pairs |

**Safe to claim**, with "to our knowledge":

1. A falsifiable, per-pixel spectral-physics model of NIR's marginal utility in RGB–NIR segmentation, verified against cross-fitted dense counterfactual utility.
2. The first held-out-condition and cross-camera evaluation protocol for RGB-X segmentation fusion gates, comparing physics-constrained, learned, condition-token and physics-input-MLP gates.
3. A physically parameterised IDD-AW-C with matched control pairs, validated against real conditions.
4. The first controlled benchmark of modern RGB-X architectures on IDD-AW. The Paper A audit rated this novel as infrastructure ([Paper A audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_novelty_audit.md>)).

**Not safe until shown:** "physics gates generalise better", "illuminant-aware fusion works on real data", and any IDD-AW SOTA.

**Must not claim:**

- First fusion robust to unseen weather (Seeing Through Fog).
- First held-out-weather protocol (DCDA).
- First wavelength-aware NIR corruption (Bishoff).
- First supervised per-pixel reliability gate (RAF, RSFNet).
- First descriptor-gated fusion (SRD, Seeing Through Fog, CAFuser).
- First counterfactual or leave-one-modality-out utility, or first cross-fitted fusion targets (2608.15261, TIER-MoE, G-Blend).
- "Regret rate" or gate–utility Spearman as new metrics.
- That learned gates generally collapse. The only evidence is backbone-conditional prostate MRI ([arXiv 2604.10702](https://arxiv.org/abs/2604.10702)).
- That NIR "penetrates" fog.

### Data preparation and the instrument that measures utility

**Data.** A′ uses the same IDD-AW pipeline and split as ALT-1′, with the full 26 level-3 classes. Report mIoU, SmIoU with the script's 8 traffic-participant classes, SmIoU with the paper's default important set (stating its members), and the mean safe-IoU over the 8 traffic-participant classes, all per condition. Train at 1024×768 with random scale 0.5–2.0, 768×768 crops and horizontal flips. Infer at 1024×768, upsample logits to native resolution, and state this explicitly, since prior work did not ([Paper A blueprint](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_experiment_blueprint.md>)).

**Host.** StitchFusion-B2 with NIR modality dropout. Per sample: keep both modalities 0.5, drop NIR 0.35, drop RGB 0.15, where "drop" means a zero (mean) stream. Recipe:

| Setting | Value |
|---|---|
| Optimiser | AdamW, lr 6e-5, weight decay 0.01 |
| Schedule | Poly 0.9 with 1.5k warm-up; 40k iterations (20k for fold models) |
| Batch | 2 at 1024×768 or 4 at 768² |
| Precision | AMP |
| Loss | CE with ignore 255 |

Gate insertion points are documented in the blueprint: the stage outputs before `feature_cross` in `stitchfusion.forward`, and `x{k}_f` before `self.FRMs[k-1]` in CMNeXt. Report full-modality mIoU with and without dropout, because the M3L paper found that dropout hurt RGB-D models ([arXiv 2304.10756](https://arxiv.org/abs/2304.10756)).

**The utility instrument.** This follows the blueprint's recipe, but is framed as measurement:

1. **Folds.** K = 2 sequence-grouped folds, stratified by condition, with 2 seeds each.
2. **Raw utility.** For every training image, take the fold model that never saw it and compute, at H/4, the per-pixel u_s = CE(RGB, ∅) − CE(RGB, NIR).
3. **Seed null.** n = u₁ − u₂. Estimate σ_null as a robust SD (1.4826·MAD) of n/√2, binned by GT class, condition and CE decile.
4. **Target.** z = ū/σ_null, and the target y = Φ(z).
5. **Exclusions.** Pixels where both CEs are below 0.05 get weight 0.
6. **Validity checks.** Report the share of pixels with |z| > 1.96 and the split-half superpixel reliability ρ(u₁, u₂). Show in-sample vs out-of-fold histograms; in-sample u is near zero because training CE approaches zero, which is why cross-fitting is necessary.

Storage is about 340 MB in float16, and the forward passes cost under 1 GPU-hour.

### Four physical factors, each with a prediction that can fail

**Descriptors** (`tools/physics_descriptors.py`, at H/4 on the GPU). About 12 channels:

| Channel | Definition |
|---|---|
| Per-band noise σ_c(x) | From a per-image Huber fit of var = a·mean + b on low-gradient 8×8 patches. Validate against Foi's ClipPoisGaus on 50 images ([Foi's page](https://webpages.tuni.fi/foi/sensornoise.html)) |
| log CNR per band; ΔCNR = log CNR_N − log CNR_Y | Local contrast-to-noise; ΔCNR is the most direct "NIR more informative here" cue |
| Implied Ångström exponent q̂ | From dark-channel transmissions for RGB and NIR, clamped to [−0.5, 3] |
| log(N/Y) and an NDVI-like (N−R)/(N+R) | Ratio and vegetation/material cue |
| Saturation, glare and NIR-only saturation masks | Glare and bloom cues |
| Illuminant ratio | NIR/VIS ratio on lit, non-vegetation surfaces |
| Material prior | Training-time only, to avoid circularity; at inference only class-agnostic ratios are used |

All descriptors assume 8-bit gamma-encoded PNGs, which is unverified. Log the inverse-sRGB step and ablate it.

**Factors and pre-registered predictions:**

| Factor | Pre-registered prediction | Real-data test | Controlled test |
|---|---|---|---|
| F1 scattering | Utility ≈ 0 where q ≈ 0 (Mie fog); utility rises with q | q̂ histogram per IDD-AW condition: fog ≈ 0 if it is true fog. Indo-Gangetic winter aerosols may make some "fog" haze ([WCRP abstract](https://www.wcrp-climate.org/conference2011/abstracts/C7/Kedia_C7_M20B.pdf)) | IDD-AW-C haze-spectral vs haze-flat at matched visibility |
| F2 illuminant | Utility is higher under halogen/HPS than under white LED | Within-image: headlight-lit vs LED-streetlight-lit regions in real low light, using student-made light-source labels | IDD-AW-C LED-like (k_N = k/4) vs halogen-like (k_N = k) |
| F3 photon budget | Utility rises monotonically with ΔCNR; the physics gate extrapolates to held-out low light | LOCO fold with low light held out, binned by σ | IDD-AW-C low-light severities |
| F4 material (ex-Paper C) | Per-class predicted unique-NIR information rank-correlates with per-class fusion gain | Spearman over IDD-AW classes, with 3 seeds of RGB-only vs fused hosts | Hyperspectral City V2 band integration |

**Running F4.** Hyperspectral City V2 has 128 bands over 450–950 nm, 19 Cityscapes classes and lossy H.264 compression ([dataset audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/wave1_datasets_audit.md>)). The steps:

1. Integrate its per-pixel spectra through approximate JAI bands (B/G/R centres about 465/540/615 nm; NIR about 850 nm, truncated at 950 nm). The real JAI quantum-efficiency curves are unpublished.
2. Estimate per-class unique-NIR information. Validate the estimator on synthetic ground truth first; a reviewer in an ICLR 2026 thread demanded exactly this for MI claims ([venues note](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/venues_figures_and_paperC.md>)).
3. Map Hyperspectral City V2's classes onto IDD-AW's.
4. Pass condition: ρ ≥ 0.5 with p < 0.05. With n ≈ 19–26 classes this is under-powered, so pre-register that a null result goes into a section, not the headline.
5. Describe this as integrating measured spectra through band curves, never as NIR→RGB conversion.

**Light-source labels for F2.** Reuse Paper B's protocol, about 3–4 person-days ([Paper B blueprint](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperB_experiment_blueprint.md>)). Two annotators label the dominant and secondary source on all ~1,000 low-light images (Label Studio, with RGB, NIR and a ratio map side by side, 10–15 s per image), and region boxes are drawn on 200 images. The target agreement is Cohen's κ ≥ 0.6; below that, merge classes into "NIR-rich" and "NIR-poor white". As a physical validation, a ratio-only classifier should reach AUC ≥ 0.75 against the human labels. India had about 13.1 million LED streetlights by November 2024 ([Parliament reply](https://eparlib.nic.in/bitstream/123456789/779754/1/AU3430.pdf)). IDD-AW's night frames are probably LED-dominated, so NIR-rich strata may be small.

### Gate arms, the leave-one-condition-out experiment, and the corruption suite

**Gate arms.** All gates are late, logit-level and swappable on a frozen host:

ŷ = g·f(RGB, NIR) + (1 − g)·f(RGB, ∅)

The Bayes-optimal gate is therefore 1[E(u given x) > 0]. This makes the decisive experiment cheap: gate heads train in minutes.

| Arm | Description | Trained on |
|---|---|---|
| G0 | Always fuse (g ≡ 1) | — |
| G1 | Learned content gate, task loss only | Segmentation loss |
| G2 | Condition-token gate over the 3 seen conditions (CAFuser / 3D-LRF style) | Out-of-fold u |
| G3 | Unconstrained MLP on [image features + φ] | Out-of-fold u |
| **G4** | **Physics gate:** sign-constrained logistic (or monotone lattice) on φ = {ΔlogCNR, per-band σ, q̂, illuminant ratio, material prior, NIR-only saturation} | Out-of-fold u |
| G5 | Mis-specified physics: G4's form with per-band σ shuffled, λ ordering swapped and the q̂ sign flipped | Out-of-fold u |
| G6 | Oracle (held-out true u) | Upper bound only |
| G-img / G-cls (optional) | Image-level and class-level gates | Granularity controls from the blueprint |

**LOCO protocol.** For each held-out condition c ∈ {rain, fog, snow, low light}:

1. Train the host on the other three conditions with 3 seeds, plus 2 cross-fit models (1 seed) for out-of-fold u.
2. Fit G0–G6 on the seen conditions.
3. Score on c's val images, using u_test computed with the frozen dropout host and val labels.

**Endpoints.** The primary endpoints are Spearman ρ(g, u_test), sign-AUROC and the null-calibrated pixel NTR; mIoU and SmIoU are secondary. All endpoints get sequence-cluster bootstrap CIs.

**Pre-registered outcomes** (my heuristics from the reframe audit, to be committed before the runs):

| Label | Outcome that supports the physics account |
|---|---|
| **P-a** | Held-out fog: most pixels' \|u\| lies inside the seed null, and NTR(G4) < NTR(G1, G2) |
| **P-b** | Held-out low light: ρ(G4) − ρ(G1) ≥ 0.1, and the gap grows with darkness |
| **P-c** | IDD-AW-C at matched visibility: u and mean g(G4) are higher under spectral than flat haze and grow with severity, while G1–G3 fail to separate the pair |
| **P-d** | u and g(G4) drop under LED-like vs halogen-like light; the within-image real-data check agrees |
| **P-e** | G5 ≈ G1 (the gain is physics content, not input capacity) and G4 > G3 (the gain is physics structure, not just the inputs) |
| **Cross-camera** | Transplant G4, re-parameterised to the MCubeS FLIR NIR band (exact band unverified), and G1 into an MCubeS RGB-N host trained with dropout. Compare ρ and NTR, scoring `NIR_warped_mask` validity separately |
| **Attribution** | Partial-R² and Shapley decomposition of out-of-fold utility over the four factor groups. This answers the "the four factors collapse to ΔCNR" objection, honestly either way |

**IDD-AW-C** (`tools/make_iddaw_c.py`) is built on val only, with a fixed seed; release the code, not the images. Depth comes from Depth Anything V2 metric-outdoor, cached and clipped to [1, 300] m (the model reference is from memory; verify). Band centres are B/G/R/N = 465/540/615/850 nm, with N ∈ {800, 900} as an ablation. Attenuation follows the Kim model, β = (3.912/V)·(λ/550)^−q(V), with q = 0 below 0.5 km visibility ([Radioengineering 2010](https://www.radioeng.cz/fulltexts/2010/10_02_223_227.pdf)); the model is contested at short visibility (Ijaz 2013). Calibrate severities so RGB-only SegFormer-B2 loses about 5/10/20/30/40%, then freeze the parameters before evaluating any gate. Finally, rank-correlate model robustness on IDD-AW-C fog and low light with robustness on real IDD-AW fog and low light, or reviewers will treat the suite as synthetic.

| Corruption | Severities 1–5 | Role |
|---|---|---|
| Haze-spectral | V = 20, 10, 5, 3, 1.5 km (q = 1.3, 1.3, 1.14, 0.82, 0.58) | Physics test |
| Haze-flat | Same V, q = 0 | **Matched control** |
| Fog-Mie | V = 400, 250, 150, 100, 60 m (q = 0) | Null prediction |
| Low-light, Poisson–Gaussian | k = 1/4 → 1/64; read noise 2–8 DN; halogen-like k_N = k vs LED-like k_N = k/4 | **Matched illuminant pair** |
| Glare | Veiling-glare PSF at 5 intensities | Saturation cue |
| NIR failures | NIR-only noise, dead rows, 1–8 px misregistration, NIR dropout | g should fall to about 0 |
| Occluders | Rain streaks or snow drawn identically in all bands | Wavelength-flat control |

### Baselines, numbers and the ablation grid

A′ does not chase SOTA, but its controlled IDD-AW benchmark must sit next to published numbers, under one recipe and resolution:

| Row | Model (code) | Role | Seeds | Reference numbers |
|---|---|---|---|---|
| B0 | SegFormer-B2 RGB (mmsegmentation or StitchFusion with RGB only) | RGB lower bound | 3 | InternImage-B RGB 62/64/62/53/64 (rain/fog/LL/snow/all) |
| B1 | SegFormer-B2 NIR | NIR column | 1 | InternImage-B NIR 61/58/57/51/61 |
| B2 | SegFormer-B2 4-channel early fusion | Stacking | 3 | InternImage-B stack 66/65/63/53/67 |
| B3 | Logit average of B0 and B1 | Trivial late fusion | — | — |
| B4 | StitchFusion-B2 | Host | 3 | — |
| B5 | B4 + modality dropout | Gate host | 3 | — |
| — | CMX-B2, CMNeXt-B2 (DELIVER) | Learned-gate RGB-X | 1 / 3 | — |
| — | MMSFormer-B4, StitchFusion-B4, DINOv2-B + LoRA | Strength checks | 1 | — |
| Reported block | ICPR'24 leaderboard; SixthSense 69.51 val | Different protocol | — | 68.54 / 68.32 (64.73 SmIoU) |
| MCubeS host | StitchFusion-B2/B4 RGB-N with dropout | Cross-camera | 3 | StitchFusion 53.21 (B4 + FFMs) |

Do not retrain Mask2Former Swin-L or InternImage-B. Each took about 4 A100-days in the competition; cite them instead ([Paper A blueprint](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_experiment_blueprint.md>)). The blueprint's controlled targets, StitchFusion-B2 ≥ B0 + 1.5 mIoU and per-condition gains largest in low light and rain, serve as sanity checks rather than claims.

**Full ablation grid** (single-seed unless marked; confirmatory rows get 3 seeds):

| Group | Rows |
|---|---|
| Instrument validity | U-in vs U-oof histograms; K = 2 vs K = 3; with vs without the seed null; 1 vs 2 seeds per fold; superpixel-level targets as fallback |
| Collapse diagnostic | G1 gate SD across images; Δ mIoU when the gate is replaced by its training mean; ρ(G1, u) — reproduced on RGB-NIR rather than cited from MRI |
| Gate arms × LOCO | G0–G6 × 4 held-out conditions × 3 host seeds (confirmatory) |
| Descriptor attribution | G4 leave-one-out: −σ, −CNR, −q̂, −illuminant, −material, −glare; plus G5 |
| Gate form | Sign-constrained logistic vs monotone lattice vs unconstrained (G3) |
| Gate target loss | BCE on Φ(z) vs Huber vs soft-Spearman ([torchsort](https://github.com/teddykoker/torchsort)) vs hard sign |
| Linearisation | Inverse-sRGB vs none for all descriptors |
| Band-centre sensitivity | λ_N ∈ {800, 850, 900} in descriptors and IDD-AW-C |
| IDD-AW-C | All 7 families × 5 severities for G0–G5; matched-pair contrasts |
| Cross-camera | G1 vs G4 transplant on MCubeS |
| Material factor | Per-class predicted vs measured; Hyperspectral City V2 estimator validation |

**Statistics.** Use a paired cluster bootstrap over val sequences with B = 10,000; the number of val sequences is unknown today and must be derived after download. Apply Holm–Bonferroni over a family committed in week 5: P-a, P-b, P-c, P-d, P-e (G4 vs G3), P-e (G5 vs G4), cross-camera ρ, material ρ, and B5 vs B0 and B5 vs B2 on mIoU, for 10 tests in all. Compute Spearman per image and bootstrap it over sequences, and state the minimum detectable effect from B0's 3-seed SD in week 2.

The kill rule from the reframe audit: if G4 does not beat G3 on held-out ρ and NTR in at least 3 of 4 folds with CIs excluding 0, the generalisation headline is dropped. The paper is then reported as a falsification study.

### Figures and tables for a 13–14 page journal paper

| Item | What it shows |
|---|---|
| Fig. 1 | Held-out fog and low-light frames: RGB, NIR, utility map u (red = NIR helps, blue = hurts), the learned gate (near-constant if it collapses) and the physics gate |
| Fig. 2 | Instrument and protocol: cross-fitting → u → seed null → Φ(z); the swappable late gate on a frozen host; the LOCO and cross-camera design |
| Fig. 3 | Instrument validity: in-sample vs out-of-fold histograms, split-half reliability, and the share of \|z\| > 1.96 per condition |
| Fig. 4 | **Physics falsification:** mean u and g vs severity for haze-spectral, haze-flat, Fog-Mie and NIR failure; LED-like vs halogen-like; q̂ histograms per real condition |
| Fig. 5 | LOCO forest plot: ρ, sign-AUROC and NTR per held-out condition per gate arm, with CIs |
| Fig. 6 | Variance decomposition of utility by factor group |
| Fig. 7 | Material factor: predicted unique-NIR vs measured per-class ΔIoU, with ρ and CI |
| Fig. 8 | Real illuminant test: violin of the NIR/VIS ratio per labelled source; u in headlight vs LED-lit regions |
| Fig. 9 | Cross-camera transplant to MCubeS |
| Fig. 10 | Qualitative grid: 4 conditions × [RGB, NIR, GT, B0, B5, G1, G4, gate maps] |
| Fig. 11 | Gate-collapse diagnostic |
| Tab. 1 | IDD-AW RGB-X benchmark (3 seeds; mIoU, both SmIoU sets, per condition) plus the reported block |
| Tab. 2 | Instrument statistics per condition |
| Tab. 3 | LOCO main table: arms × folds |
| Tab. 4 | Descriptor leave-one-out and G5 |
| Tab. 5 | IDD-AW-C per corruption family, with the real-condition validation correlation |
| Tab. 6 | Cross-camera MCubeS |
| Tab. 7 | Descriptor and gate overhead in ms |
| Supplement | Safe-mIoU implementation notes; IDD-AW-C parameters; light-source annotation guide and κ; per-class tables; pre-registration document |

**Journal length.** The page budget matches IEEE TIP (13 pages at first submission) or TCSVT (14 pages) ([SPS author information](https://signalprocessingsociety.org/publications-resources/information-authors); [TCSVT](https://ieee-cas.org/node/3113)). TMM's 10-page first-submission limit is too tight.

**Compute: about 480–560 GPU-hours** (my estimate, built from the blueprint's per-run times):

| Block | GPU-h | Note |
|---|---|---|
| Benchmark | ~200 | Shared with ALT-1′'s P3 |
| Pre-test host and fold models | ~50 | |
| LOCO | ~160–240 | 4 folds × (3 hosts + 2 cross-fit models) |
| MCubeS host | ~40 | |
| IDD-AW-C build and inference | ~15 | |
| Descriptors | ~5 | |
| Gate heads | Negligible | |

## A 40-week calendar with an exit at every gate

**No plan can guarantee better-than-published results**, and nothing here should be read as a promise. The thesis roadmap says the same thing in its first lines ([roadmap](</home/user/lowlight/research/PUBLICATION_ROADMAP.md>)). Every target number above is a heuristic. The notes contain no experiment run on these ideas, and IDD-AW's per-condition NIR gains are small.

What the plan can do is make failure cheap and recoverable. Each paper's most uncertain question is answered first, with inference-only or single-seed runs; the pass thresholds are written down before the data arrive; and every failure branch still ends in a submission. The two gates that decide whether ALT-1′ is an A\* paper at all (G0 and GNG-1) cost about **155 GPU-hours**, roughly 8% of the GPU time available through mid-March, and resolve by **December 13, 2026**.

### Week-by-week plan

Week 1 starts Monday, November 2, 2026. The "thesis" column summarises the roadmap's own commitments, which take priority.

| Week (dates) | Shared infrastructure | ALT-1′ (A\*) | A′ (Q1) | Thesis load |
|---|---|---|---|---|
| W1 (Nov 2–8) | Register for IDD-AW (24-hour token). Download IDD-AW, MCubeS, FANVID, Cityscapes, ACDC. Write the label converter and evaluator; verify 3430/475 counts and masks on 20 overlays. Measure memory and speed. Email SegBridge, SpectraDINO and HSVF (VNHD) authors | Choose or start the Cityscapes source model; verify Vireo, CAT-Seg and FC-CLIP code | — | SCI fix; MAET split re-runs |
| W2 (Nov 9–15) | B0 / B2 / B4, 1 seed; 19-class mapping; minimum detectable effect | **E0** (A0–A3, inference only) → **gate G0** | — | Paper 2 cross-fitted teachers |
| W3 (Nov 16–22) | — | Alignment screening: M0, M2 on R-UDA | All-condition dropout host (B5) + K = 2 fold models × 2 seeds | DarkFace / NOD |
| W4 (Nov 23–29) | — | Screening: M1, M3, SpectraDINO recipe | Utility, null, z; probe model | — |
| W5 (Nov 30–Dec 6) | Commit both pre-registration files | Fusion rules F0–F5 on screening students | **Gate GO-A1** (instrument validity) | — |
| W6 (Dec 7–13) | — | **Gate GNG-1** | Descriptors φ; q̂ per real condition | Paper 2 GNG-1 |
| W7 (Dec 14–20) | — | R-DG (FANVID-only) student; A17 target-exposure control | **Gate GO-A2** (physics pre-test) | Paper 2 GNG-2 |
| W8–9 (Dec 21–Jan 3) | Holiday: queue 3-seed runs overnight | Set up the OV models; 3-seed key alignments | IDD-AW-C build and severity calibration (co-author); light-source labelling round 1 | Paper 1 P0–P4 runs |
| W10 (Jan 4–10) | — | TTA stage; TENT / CoTTA | **Gate GO-A3** (κ, AUC); LOCO folds 1–2 | — |
| W11 (Jan 11–17) | — | **Gate GNG-2** (headline protocol) | LOCO folds 3–4; Hyperspectral City V2 material pilot → **gate GO-A4** | — |
| W12–13 (Jan 18–31) | — | OV with the NIR plug-in; A18 depth vs NIR | Gate arms G0–G6 on all folds | Paper 1 scaling and interventions |
| W14–15 (Feb 1–14) | — | P3 supervised, A20 label fraction, MCubeS, A3 HAQAGen control | IDD-AW-C evaluation of the arms | Paper 2 full grid |
| W16 (Feb 15–21) | — | **Gate GNG-3: results freeze** | — | — |
| W17–19 (Feb 22–Mar 14) | — | Figures and first draft | GPU yields to the thesis | **Paper 1 submission (Mar)** |
| W20–22 (Mar 15–Apr 4) | — | Draft v2; internal review; scoop re-check | MCubeS host; cross-camera transplant | **Paper 2 → ACM MM (~Apr 1)** |
| W23–26 (Apr 5–May 2) | — | Final text, supplement, NeurIPS abstract registration | LOCO 3-seed confirmation → **gate GO-A5**; attribution analysis | — |
| W27 (May 3–9) | — | **Submit to NeurIPS 2027** (estimated; the 2026 cycle had its abstract deadline May 4 and paper May 6) | Remaining runs | — |
| W28–31 (May 10–Jun 6) | arXiv preprints, with a cross-citation plan | Code release preparation | Figures; Holm tests | Paper 3 deferred |
| W32–36 (Jun 7–Jul 11) | — | NeurIPS rebuttal when reviews arrive | Writing | ACM MM notification (~Jul) |
| W37–40 (Jul 12–Aug 8) | — | — | **Submit A′ to IEEE TIP or Information Fusion** | Paper 2 → AAAI-28 if rejected |
| After Sep 2027 | — | If NeurIPS rejects (2026 decisions came Sept 24): CVPR 2028 (~mid-Nov 2027) | Journal revision | — |

### Gates, costs and fallbacks

| Gate | When | Cumulative GPU-h [est.] | Pass criterion | If it fails |
|---|---|---|---|---|
| **G0** (ALT-1′) | W2, Nov 15 | ~5 | RGB source-only on IDD-AW val (19 classes) within ±5 of SegBridge's 58.4 baseline. Informative: NIR-in-red ≥ 0.6× RGB mIoU makes the raw-NIR TTA bridge viable | Fix the class mapping or source model; no training until it passes. If NIR-in-red is poor, use the aligned student only |
| **GO-A1** (A′) | W5, Dec 6 | ~60 | ≥ 15% of valid pixels with \|z\| > 1.96; split-half superpixel ρ ≥ 0.3; out-of-fold ≠ in-sample; condition ordering LL/rain > fog/snow | K = 3 and superpixel targets. If still failing, A′ shrinks to a benchmark + IDD-AW-C paper (about 5–6/10 Q1; T-ITS or Pattern Recognition) |
| **GNG-1** (ALT-1′) | W6, Dec 13 | ~150 | Mean fusion (F2) ≥ RGB-only + 1.5 mIoU on val with CI lower bound > 0, **and** best mask ≥ unmasked + 0.5 overall or + 1.5 on low light | (a) No fusion gain: stop ALT-1′ as an A\* paper, write a short Q1/WACV note that label-free NIR adds little to frozen VFMs, and promote A′ to the A\* slot (AAAI-28). (b) Fusion passes but masking does not: drop the masking claim; ALT-1′ becomes a Q1 or WACV 2028 Applications paper, and A′ takes the A\* slot if GO-A2 passed |
| **GO-A2** (A′) | W7, Dec 20 | ~80 | Physics factors explain ≥ about 10% of out-of-fold utility variance (partial R²), or q̂ separates fog from other conditions, or P-c passes on IDD-AW-C | Drop the generalisation headline; A′ becomes a characterisation/falsification Q1 with honest nulls |
| **GO-A3** (A′) | W10, Jan 10 | 0 GPU; 3–4 person-days | κ ≥ 0.6 and ratio AUC ≥ 0.75 | Merge classes; keep only the synthetic LED/halogen pair for F2 |
| **GNG-2** (ALT-1′) | W11, Jan 17 | ~400 | P1 ≥ 69.8 under a matched protocol (or ≥ best reproduced RGB-only continual TTA + 2.0 if SegBridge clarifies 65.1 or a different backbone), **and** ≥ A17 + 1.0; OV plug-in ≥ + 1.5 for 2 of 3 OV models | Skip NeurIPS; add ViT-L and more unlabeled data; submit to AAAI-28 (~late Jul) or CVPR 2028 |
| **GO-A4** (A′) | W11, Jan 17 | ~5 | Material ρ ≥ 0.5, p < 0.05 | Report the per-class null as a section |
| **GNG-3** (ALT-1′) | W16, Feb 21 | ~650 | The primary Holm-family tests (full vs A0, full vs A17, A8 vs A5) pass with 3 seeds | Submit to AAAI-28 instead of NeurIPS, with 5 more months of work |
| **GO-A5** (A′) | W26, May 2 | ~480 | G4 > G3 on held-out ρ and NTR in ≥ 3 of 4 folds, CIs excluding 0 | Publish as a falsification study; the IDD-AW benchmark and IDD-AW-C keep it Q1-viable |

The pass thresholds on 10% partial R², a ρ margin of 0.1, +1.5 mIoU and ±5 sanity bands are heuristics drawn from the notes' own suggestions. They are not literature values. The gates interlock so that one paper's failure strengthens the other's case. If ALT-1′ fails GNG-1, A′ is already six weeks into its instrument and takes the A\* slot with the reframe audit's ceiling of 7. If A′'s physics fails GO-A2, ALT-1′ is unaffected, because it uses physics only in one ablation row.

### Venues and deadlines

All 2027–28 dates are estimates from 2026 cycles; confirm them when they are announced ([venues note](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/venues_figures_and_paperC.md>)).

| Paper | Primary target | Deadline | Why | Fallback chain |
|---|---|---|---|---|
| **ALT-1′** | **NeurIPS 2027** | ~early–mid May 2027 [EST]. 2026 had its abstract on May 4 and full paper on May 6; 9 content pages; error bars expected ([NeurIPS 2026 CFP](https://neurips.cc/Conferences/2026/CallForPapers)) | Vireo (NeurIPS'25) and FuseAdapt (NeurIPS'26) are exactly this genre; the date falls after the thesis's ACM MM deadline | GNG-2 fails → AAAI-28 (~late Jul 2027; 7 content pages) ([AAAI-27 call](https://aaai.org/conference/aaai/aaai-27/main-technical-track-call/)). NeurIPS rejects → CVPR 2028 (~mid-Nov 2027) |
| **A′** | **IEEE TIP** (13 pages initially; IF 13.7) or **Information Fusion** (IF 15.5; page limit unverified) | Rolling; target end of Jul–Aug 2027 | Physics-of-imaging plus fusion; long enough for 11 figures | IEEE TCSVT (14 pages); IEEE T-ITS (driving; at most 3 review rounds ([T-ITS](https://ieee-itss.org/pub/t-its/))). If ALT-1′ fails GNG-1 and LOCO is clean: AAAI-28 |
| Paper B (optional) | WACV 2028 Evaluations & Datasets track | Round 1 ~late Jun, Round 2 ~late Aug 2027 [EST] | The track explicitly wants papers that "compare evaluation designs" ([WACV 2027 CFP](https://wacv.thecvf.com/Conferences/2027/CallForPapers)) | Thesis chapter; submit only after thesis Paper 1 and cite it |

ALT-1′ should avoid three venues. ACM MM 2027 is where the thesis's Paper 2 already goes, and MM publishes its reviews, so a rejection leaves a public record. ICCV 2027 falls in early March, the same weeks as the thesis's Paper 1 submission. CVPR 2027 closes November 16, 2026 ([CVPR 2027 CFP](https://cvpr.thecvf.com/Conferences/2027/CallForPapers)), too close for a new paper. AAAI-28 also closes before NeurIPS decisions, so a NeurIPS submission cannot fall back to it in the same cycle. CVPR 2028's reciprocal-reviewer quota caps unpublished groups at one paper, but HAQAGen (WACV 2026) should make the group eligible.

### Capacity: the calendar works only with three concessions

The GPU arithmetic is tight but feasible: about 1,870 GPU-hours are available through March 15 at 60% utilisation. Demand by then:

| Item | GPU-h by Mar 15 |
|---|---|
| ALT-1′ (most of its total) | ~650 |
| A′ (its first half) | ~250 |
| Thesis Paper 2 | ~550 |
| Thesis Paper 1 | Unquantified in the roadmap |

The binding constraint is one student's attention across thesis Papers 1–2, ALT-1′, A′ and the annotation work. Three concessions make it workable: defer thesis Paper 3, which is already conditional on a pilot; hand the IDD-AW-C build and light-source labelling to a co-author; and accept that A′ is written after the NeurIPS deadline. If the advisor will not make these concessions, the honest alternative is to run only ALT-1′ through May and start A′ in June, with a journal submission around October 2027.

## Two dozen other candidates lost on specific evidence

| Candidate | Score (A\* / Q1) | Decisive reason it lost | Where its useful part went |
|---|---|---|---|
| Original Paper A (counterfactually supervised, physics-informed fusion gates as a method) | 5 / 7 | Pre-empted at sample level by TIER-MoE, 2608.15261 and GAUGE; RAF supervises per-pixel reliability; its "regret rate" and gate–utility metrics already exist; gains about +1 mIoU; title collision ([Paper A audit](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/paperA_novelty_audit.md>)) | Reframed as A′ |
| Paper B (NIR-guided enhancement protocol study + no-regret enhancer) | 3–4 / 6 | Reuses thesis protocols and metric; no-regret hole; small passive-NIR gains (IDD-AW low light 62 → 63 with stacking); nearest the colorization boundary in pitch-dark regions | Thesis chapter or WACV E&D; its light-source protocol feeds A′'s F2 |
| Paper C (camera-conditioned spectral PID + conformal) | 4 / 6.5 | Rank test with 19–26 classes is under-powered; two loosely coupled halves; overlaps A; the Galway group is active in this exact space | Material factor F4 in A′; conformal half dropped |
| ALT-1 as literally stated (pure OV from NIR) | 5 / 7 | "SpectraDINO + Talk2DINO"; OpenESS-for-NIR; weak "open vocabulary" on 26 mostly familiar classes | Rescoped into ALT-1′ |
| ALT-2 (cross-spectral source-free TTA) | 5 / 7 | Night-TTA, MM-TTA, DASP, BriMPR; AdaPGC's Nov 2, 2026 extension owns the "reliability shift" framing | Test-time stage of ALT-1′ (A16) |
| ALT-3 (label-efficient RGB–NIR co-training) | 3 / 5 | A dataset port of M3L and SpiderMesh; supervision transfer dates to 2016 | One table in ALT-1′ (A20) |
| ALT-4a (RGB–NIR disagreement as failure detector) | ~5 / 6–7 | Prior art not fully searched; reads as uncertainty quantification with NIR tangential | Analysis row A21 in ALT-1′ |
| ALT-4b (thermal-privileged training, RGB-NIR deployment) | 4 / 6 | Learning with privileged information is old; TRNT already handles uncertain modalities | Dropped |
| Depth/stereo; road wetness | 3–4; infeasible | Kim & Baek and MS2 exist; no labelled wetness data, and silicon NIR misses the strong water bands | Dropped |
| S1 hyperspectral RGB+NIR simulator + pretraining | 3.5 / 6 | Fog is wavelength-flat; ICVL is 201 non-driving scenes; reads as engineering ([spectral note](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/wave1_spectral_physics_imaging.md>)) | Physics reused in IDD-AW-C |
| S2 complementarity maps + prior | 4 / 6.5 | Same as Paper C | F4 |
| S3 RGB-IR demosaicking + decontamination | 3 / 5 | No real raw data without hardware; no benchmark | Dropped |
| S4 weather-aware NIR band co-design | 4.5 / 6.5 | Simulation-only conclusions; the answer may be trivial; Galway scoop risk | Dropped |
| S5 ambiguity-aware conformal sets | 3.5 / 6 | Physics looks cosmetic; exchangeability breaks across weather | Dropped |
| S6a raw RGB-IR perception; S6b differential attenuation as a gate cue | 4.5 / 6.5; 3.5 / 5.5 | S6a: incomplete prior-art check, simulation-heavy, and IDD-AW's prism camera has no NIR contamination to test it. S6b: the cue vanishes in fog and rain | S6b's q̂ survives as A′'s F1 falsification test |
| R1 task-preserving NIR-guided enhancement; R5 "when does NIR help" benchmark | 5 / 7; 4 / 7–8 | Restoration overlaps the thesis topic and sits near colorization; "TarDAL for NIR"; a benchmark without a method ([restoration note](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/wave1_nir_assisted_restoration.md>)) | Became Paper B, then demoted |
| R2 learning from unreliable references | 5 / 7 | High scoop risk from the NAID group; needs a new dynamic-scene dataset; closest to the idea file's colorization Direction II | Dropped |
| R3 spectrally grounded structure transfer | 4 / 6 | One NIR band cannot pin down reflectance; learned gates are near ceiling (SFM is +0.23 dB over summation) | Dropped |
| R4 illuminant-spectrum-aware NIR guidance | 6 / 7 | No illuminant labels and no spectrometer; as restoration it collides with the thesis | F2 in A′, as perception rather than restoration |
| R6a NIR guidance for rain/snow; R6b noisy-guide passive NIR | 5 / 7; 6 / 7 | No clean targets on real adverse or passive-night data | Dropped |
| Wave-1 perception C1–C7 | 3–6 | C4 privileged NIR 3; C5 hierarchical conformal 4–5 (capstone overlap); C6 synthetic NIR 4 (data-processing objection, translation-adjacent) ([perception note](</home/user/nirdiscusspaper/research_notes/RGB NIR paper plans beyond colorization/wave1_rgbnir_perception_sota.md>)) | C1 and C2 → A′; C3 → ALT-2 → ALT-1′; C7a → F2; C7b → shared infrastructure |

A pattern runs through the losers. Every restoration idea (R1–R6, Paper B) either collides with the student's own enhancement thesis or drifts toward generating colour from NIR, the two things the brief ruled out. Every pure-physics or sensor-design idea (S1, S3, S4, S6a) needs hardware or lives only in simulation. Every uncertainty idea (S5, C5, ALT-4a) makes NIR tangential. The two survivors are the ones that use real, pixel-aligned NIR as a *perception input* and make claims a public benchmark can check.

## Conclusion

The portfolio is best read as a claim and its explanation, not as two independent bets. ALT-1′ claims that a real NIR band adds value to a frozen RGB foundation model without a single NIR label. A′ explains where that value comes from and when a gate should refuse it. They share one data pipeline and one evaluator, and both ask whether NIR is reliable at a pixel. That shared reliability vocabulary is exactly why claim ownership has to be written down now: the physics account, the benchmark and IDD-AW-C belong to A′; masked label-free alignment belongs to ALT-1′; the cross-fitted estimator belongs to the thesis.

The decisive risks are therefore practical rather than conceptual, and they are front-loaded: a soft headline number, a protocol trap between adaptation and generalisation, and one student's capacity are all settled in the first two weeks by two emails, one regime table and one conversation with the advisor. After that, the single most informative experiment in the plan is ALT-1′'s first fusion gate, which costs about 150 GPU-hours. If label-free NIR fusion cannot beat RGB-only by 1.5 mIoU on IDD-AW, the student learns it before the holidays and can promote A′ into the A\* slot without having lost the season.
