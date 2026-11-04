# Alternative A*-level non-colorization RGB–NIR ideas: novelty audit (searched up to 2026-11-03)

**Method.**
- Ran arXiv API sweeps (cs.CV; abstract and title fields; 2016 to Nov 2026) on:
  - NIR × open-vocabulary / CLIP / DINOv2 / zero-shot;
  - thermal/IR × CLIP / VLM;
  - open-vocabulary × multimodal and adverse conditions;
  - multimodal TTA (titles);
  - NIR × test-time / source-free / UDA;
  - semi-supervised × RGB-T / RGB-D / multimodal segmentation;
  - NIR road-surface;
  - RGB-NIR stereo/depth;
  - cross-modal distillation × IR segmentation.
- Read in full or in part: SpectraDINO (arXiv HTML), Vireo (arXiv HTML), the SegBridge CVPR'26-Findings supplement (PDF) and the AetherVision-Bench PDF. Read about 25 abstracts through the arXiv API.
- Two WebSearch calls worked.
- "(mem)" marks a fact taken from memory and not re-verified this session.
- This file builds on `wave1_rgbnir_perception_sota.md`, `wave1_datasets_audit.md` and `paperA_novelty_audit.md` (Paper A: A\* 5/10, Q1 7/10). It does not repeat them.

**Bottom line.**
- **No alternative is verifiably open at novelty ≥ 7 for A\* on concept alone.**
- The strongest is a **re-scoped ALT-1, called ALT-1′ below**. The method adds NIR to an already-trained, frozen RGB foundation segmenter with no NIR labels. A NIR encoder is aligned to the frozen RGB VFM feature space on unlabeled pairs, using *reliability-masked* distillation. The two streams are then fused per pixel at test time.
- It is evaluated in the domain-generalisation setting (Cityscapes → IDD-AW, against SegBridge and Vireo) and the open-vocabulary setting.
- ALT-1′ scores **6/10 A\*, with a ceiling of about 7 if the headline results hold**, and **8/10 Q1**. That beats the current Paper A (5–6).
- It also has the clearest path to "better than SOTA" numbers on a public benchmark: Cityscapes → IDD-AW, where the best published RGB-only continual-TTA figure is SegBridge's 69.8.
- ALT-2 is best folded into ALT-1′ as its test-time stage. ALT-3 and the ALT-4 options are weaker.

---

## Q1. ALT-1: NIR-aware VFM adaptation for open-vocabulary / zero-shot adverse-condition segmentation

### Takeaway
I found **no paper doing open-vocabulary or zero-shot segmentation from NIR or RGB+NIR**, and none evaluating OV segmentation on IDD-AW. However, every ingredient has strong precedent:
- The paradigm of distilling an RGB foundation model into a new sensor using paired, unlabeled images: Gupta CVPR'16, ImageBind CVPR'23, OpenESS CVPR'24, OVOSE.
- The NIR-specific DINOv2 alignment: SpectraDINO, May 2026.
- Text alignment of DINOv2: Talk2DINO ICCV'25, dino.txt.
- Sensor fusion for OV segmentation under clouds/haze: MM-OVSeg CVPR'26.
- OV domain generalisation for adverse driving: Vireo NeurIPS'25.

As literally stated, ALT-1 scores about **5/10 A\*** and **7/10 Q1**. The obvious attack is "SpectraDINO + Talk2DINO, applied to NIR".

### Cited Findings
**Closest prior work, ranked by threat**

**SpectraDINO** (Nalcakan et al., Yonsei; arXiv 2605.02258, May 2026, preprint, no venue) — [arXiv HTML](https://arxiv.org/html/2605.02258)
- Method:
  - Frozen DINOv2 ViT-B/L teacher.
  - Shared-ViT student with modality-specific stems, modality embeddings and per-block bottleneck adapters (not LoRA).
  - Losses: CLS cosine distillation, symmetric RGB–IR InfoNCE, patch alignment, and a neighbourhood KL over a 65,536-entry bank.
- Training data: 100K pairs each from MS2 (RGB-NIR), RASMD (RGB-SWIR) and IR500K (RGB-LWIR).
- NIR result: **MCubeS RGB+NIR 46.76 mIoU (ViT-L)**, well below CMNeXt's four-modality 51.54.
- **No open-vocabulary or zero-shot evaluation. IDD-AW is not used. FANVID, IDD-AW, RANUS and EPFL are not used.**
- Its own limitations: RGB acts as the intermediary between IR bands, and fusion was task-agnostic.

**OpenESS** (CVPR 2024 Highlight, arXiv 2405.05259) — [arXiv](https://arxiv.org/abs/2405.05259)
- Transfers CLIP knowledge from image-text pairs to event streams, using frame-to-event contrastive distillation plus text-to-event semantic consistency.
- Reaches 53.93 / 43.31 mIoU on DDD17 / DSEC-Semantic "without using event … labels".
- This is the same recipe as ALT-1, with events instead of NIR.

**OVOSE** (arXiv 2408.09424, 2024) — [arXiv](https://arxiv.org/abs/2408.09424)
- "First open-vocabulary semantic segmentation algorithm for event cameras", via knowledge distillation from an image foundation model.

**ImageBind** (CVPR 2023, arXiv 2305.05665) — [arXiv](https://arxiv.org/abs/2305.05665)
- "Only image-paired data is sufficient to bind the modalities". It extends zero-shot capabilities to depth and **thermal**, but not NIR.

**Gupta, Hoffman & Malik, "Cross Modal Distillation for Supervision Transfer"** (arXiv 1507.00448; CVPR'16 (mem)) — [arXiv](https://arxiv.org/abs/1507.00448)
- Transfers representations "from a large labeled modality" to "a new unlabeled paired modality".
- This is the 2016 origin of the paired-unlabeled-distillation idea. Must cite.

**Talk2DINO** (ICCV 2025, arXiv 2411.19331) — [arXiv](https://arxiv.org/abs/2411.19331)
- Maps CLIP text embeddings onto frozen DINOv2 patch features for OV segmentation.

**dino.txt** (arXiv 2412.16334) — [arXiv](https://arxiv.org/abs/2412.16334)
- LiT-style text alignment for DINOv2, at both global and dense level.

**Composition risk.** Together with SpectraDINO, these give a NIR→DINOv2→text pipeline with no new method.

**MM-OVSeg** (CVPR 2026, arXiv 2603.17528) — [arXiv](https://arxiv.org/abs/2603.17528)
- Optical–SAR fusion for "resilient open-vocabulary segmentation under adverse weather".
- SAR provides "cloud-penetrating structural cues".
- Uses cross-modal unification plus a dual-encoder fusion of multiple VFMs.
- This is the **same narrative** as "NIR penetrates haze", in remote sensing.

**Vireo** (NeurIPS 2025, arXiv 2506.09881) — [arXiv HTML](https://arxiv.org/html/2506.09881)
- Defines OV domain-generalised segmentation (OV-DGSS) with frozen VFMs plus frozen Depth Anything V2.
- Cityscapes → ACDC mIoU (Vireo vs best OVSS baseline):

  | Condition | Vireo | Best OVSS baseline |
  |---|---|---|
  | Night | 60.6 | FC-CLIP 40.8 |
  | Fog | 82.3 | SED 69.0 |
  | Rain | 76.3 | FC-CLIP 63.2 |
  | Snow | 76.2 | FC-CLIP 61.5 |

- Also evaluates DELIVER (synthetic, with predicted depth only).
- **No IDD, NIR or thermal targets.**
- Compute: about 45 GB peak memory on an A6000, batch 8, 40k iterations.

**Open-RGBT** (arXiv 2410.06626) — [arXiv](https://arxiv.org/abs/2410.06626)
- Open-vocabulary RGB-T zero-shot segmentation using visual prompts and CLIP re-scoring.

**TASeg** (IROS, arXiv 2506.21975) — [arXiv](https://arxiv.org/abs/2506.21975)
- Text-aware RGB-T segmentation built on VFMs.

**CLARITY** (arXiv 2602.07343) — [arXiv](https://arxiv.org/abs/2602.07343)
- VLM-prior-guided, condition-adaptive RGB-T fusion; 62.3 mIoU on MFNet.

**AetherVision-Bench** (Sikdar, Gandhamal, Sundaram, IISc; workshop PDF) — [PDF](https://worldmodelbench.github.io/static/pdfs/9_AetherVision_Bench_An_Open_V.pdf)
- An OV RGB-IR segmentation benchmark covering ISPRS, UAVid, IndraEye, CART, MSRS and MVSeg; the IR data is thermal.
- Evaluates OVSeg, ZegFormer, SAN and others, and reports sensor-shift degradation.
- **No NIR.**

**Other IR VLM/adaptation work**
- "From Words to Wavelengths" (arXiv 2512.15971) adapts Grounding DINO and YOLO-World to RGB-thermal few-shot detection on FLIR and M3FD — [arXiv](https://arxiv.org/abs/2512.15971).
- IV-tuning (arXiv 2412.16654): PEFT for IR-visible tasks — [arXiv](https://arxiv.org/abs/2412.16654).
- MLE-SAM (arXiv 2412.04220): mixture of LoRA experts on SAM for depth and event segmentation — [arXiv](https://arxiv.org/abs/2412.04220).
- FuseAdapt (NeurIPS 2026, arXiv 2610.21779): frozen VFM with per-modality Q/V LoRA and FFN-LoRA routing; supervised, MCubeS 57.62 per wave1 — [arXiv](https://arxiv.org/abs/2610.21779).

**Scoop checks (all negative for NIR OV segmentation)**
- arXiv sweep for NIR/near-infrared × {open-vocabulary, CLIP, foundation model, DINOv2, zero-shot} in cs.CV. Hits were only SpectraDINO, Pix2Next, iris presentation-attack detection and remote-sensing papers — [arXiv API](https://export.arxiv.org/api/query?search_query=abs:(%22near-infrared%22+OR+NIR)+AND+abs:(%22open-vocabulary%22+OR+CLIP+OR+%22foundation+model%22+OR+DINOv2+OR+%22zero-shot%22)+AND+cat:cs.CV&max_results=40).
- A web search also "didn't turn up a 2026 paper that specifically targets NIR images with CLIP-based open-vocabulary segmentation" — closest were [MM-OVSeg](https://arxiv.org/abs/2603.17528) and [TASeg](https://arxiv.org/html/2506.21975v1).

**Negative-result risk for any NIR paper**
- "Forward-Facing Near-Infrared Adds Little to Colour for Farm-Machinery Traversability" (arXiv 2609.13265, Sep 2026) — [arXiv](https://arxiv.org/abs/2609.13265).
  - With site-disjoint splits, "none of the four tested configurations (color, NIR, a luminance control, or their fusion) reliably surpasses standard color".
  - Earlier NIR gains there came from spatial leakage.

### Inferences
**ALT-1 as stated** (LoRA-aligned NIR encoder, then OV segmentation from NIR or RGB+NIR):
- **Novelty:** A\* **5/10**, Q1 **7/10**. It is the first NIR instance of an established paradigm (OpenESS / ImageBind / SpectraDINO), and the OV head is off-the-shelf (Talk2DINO / dino.txt / CAT-Seg).
- **Feasibility:** high.
  - DINOv2-B with LoRA r=8–16 fits 24 GB.
  - ViT-L needs batch 2–4 plus gradient checkpointing.
  - Paired data: FANVID 5,144 + IDD-AW train 3,430 + MS2 RGB-NIR (SpectraDINO used 100K MS2 pairs; MS2 RGB and NIR come from separate stereo rigs, so the pairs are not pixel-aligned (mem; check)) + RANUS if obtainable.
- **Problem with "open vocabulary" on IDD-AW:** its 26 level-3 classes are almost all COCO-Stuff or Cityscapes concepts. There are few truly unseen classes, so reviewers may call it "zero-shot closed-set transfer", not OV.

**Main technical hole: the teacher-degradation problem (my inference).**
- If the NIR student is trained to match RGB-teacher features on foggy or dark pairs, it learns to reproduce the *degraded* RGB features. It then adds nothing exactly where NIR should help.
- A defensible contribution is **reliability-masked cross-spectral distillation**: distil only where the RGB teacher is reliable (clear images or high-confidence/low-entropy regions, optionally gated by Paper A's physics descriptors).
- This lets the NIR student exceed the teacher in degraded regions.
- Related masking exists in RGB↔thermal UDA ([ICRA'25, 2505.06951](https://arxiv.org/abs/2505.06951) masks uncertain regions in mutual learning). It has not been used for VFM feature alignment to NIR as far as I found.

**Re-scoped ALT-1′ (recommended):** "label-free NIR plug-in for frozen RGB foundation segmenters".
- **Steps:**
  - (i) Take an RGB-only segmenter trained on Cityscapes: closed-set DINOv2 + Mask2Former, or an OV model such as Vireo or CAT-Seg.
  - (ii) Align a NIR LoRA-student to the frozen RGB VFM on unlabeled pairs, with reliability-masked distillation.
  - (iii) Fuse RGB and NIR tokens per pixel at test time using a training-free reliability score: RGB–NIR feature agreement, entropy, and optionally physics descriptors.
  - (iv) Optionally run test-time adaptation (this is ALT-2).
- **Comparators:**
  - SegBridge, Cityscapes → IDD-AW, 69.8 (Q2).
  - Vireo in the OV-DGSS protocol, adding IDD-AW as a target.
  - Supervised IDD-AW SOTA, about 68.3–68.5 test (wave1).
- **Rhetorical hook:** Vireo injects *predicted* depth, which is a function of RGB. NIR is a second *measurement*. That gives a clean data-processing-inequality argument for why a measured band should beat predicted geometry under fog and low light.
- **Novelty:** A\* **6** (ceiling 7 with strong numbers), Q1 **8**.

### Gaps
- SpectraDINO code and weights: availability not checked. If released, it is both the strongest baseline and a ready initialisation.
- Whether MS2 RGB and NIR are pixel-aligned or only rectified stereo was not re-verified (mem). This affects dense patch-level distillation.
- No published zero-shot or OV numbers exist on IDD-AW, so the expected gain is my estimate, not evidence. Vireo's ACDC night gap of +19.8 over FC-CLIP suggests large headroom in low light.
- I could not open the AetherVision-Bench venue page, so its acceptance status is unverified.

---

## Q2. ALT-2: Cross-spectral consistency for source-free test-time adaptation under weather shift

### Takeaway
- No TTA or source-free adaptation for **RGB-NIR segmentation** exists in the arXiv record I swept.
- The specific twist, a source model that **never saw NIR** using NIR as a test-time-only second view, is also unclaimed.
- But multimodal TTA is crowded:
  - Night-TTA does RGB-T cross-modal TTA for night.
  - MM-TTA and Latte++ do 2D–3D.
  - Classification-centric multimodal TTA (DASP CVPR'26, BriMPR AAAI'26 Oral, AdaPGC 2604.19093 and its Nov-2026 follow-up 2611.03232) already models "modality reliability shifts".
- Score: about **5/10 A\*** stand-alone and **7/10 Q1**. Its best use is as the test-time stage of ALT-1′.

### Cited Findings
**Prior work**
- **Night-TTA** (IEEE TAI, arXiv 2307.04470) — [arXiv](https://arxiv.org/abs/2307.04470).
  - "The first test-time adaptation (TTA) framework … for nighttime RGBT semantic segmentation without access to the source (daytime) data".
  - Its Imaging Heterogeneity Refinement handles the larger day–night gap in RGB than in thermal.
  - The source model was trained on *both* modalities.
- **Cross-spectral UDA for thermal segmentation** (ICRA 2025, arXiv 2505.06951) — [arXiv](https://arxiv.org/abs/2505.06951).
  - "Masked mutual learning … selectively transferring results between each spectral model while masking out uncertain regions", plus a prototypical self-supervised loss for night.
- **MM-TTA** (CVPR 2022, arXiv 2204.12667) — [arXiv](https://arxiv.org/abs/2204.12667).
- **Latte++ / ITTA** (arXiv 2403.06461) — [arXiv](https://arxiv.org/abs/2403.06461). Multi-modal TTA for 3D segmentation using reliable spatio-temporal voxels; it notes that the "reliable modality guidance" assumption can be violated.
- **DASP** (CVPR 2026, arXiv 2603.00574) — [arXiv](https://arxiv.org/abs/2603.00574). Diagnoses the "biased modality" via inter-dimensional redundancy and adapts it asymmetrically.
- **BriMPR** (AAAI 2026 Oral, arXiv 2511.22862) — [arXiv](https://arxiv.org/abs/2511.22862). Progressive re-alignment for multimodal TTA.
- **AdaPGC** (arXiv 2604.19093) — [arXiv](https://arxiv.org/abs/2604.19093). Extended on **2026-11-02** as "Calibrating Multimodal TTA under Reliability and Availability Modality Shifts" (arXiv 2611.03232) — [arXiv](https://arxiv.org/abs/2611.03232).
  - Explicitly targets "modality reliability shifts, which preserve modality availability but degrade information quality".
  - It is classification-centric (abstract), but the "per-modality reliability-weighted TTA" framing is now taken.
- **Pandey et al.** (ECCV 2020 Spotlight, arXiv 2006.08696) — [arXiv](https://arxiv.org/abs/2006.08696). UDA for NIR segmentation through generative latent search.
- **Texture-Shape Bias Balancing** (ECML-PKDD 2026, arXiv 2606.15072) — [arXiv](https://arxiv.org/abs/2606.15072). "First systematic study on synthetic to real domain adaptation for semantic segmentation in NIR images in the automotive domain", using diffusion-based NIR-style augmentation.

**SegBridge facts** (CVPR 2026 Findings; Jangamreddy, Baktashmotlagh, Arora; UQ / IIT Delhi). Source: [CVF supplement](https://openaccess.thecvf.com/content/CVPR2026F/supplemental/Jangamreddy_Continual_Adaptation_of_CVPRF_2026_supplemental.pdf), [poster page](https://cvpr.thecvf.com/virtual/2026/poster/41257).
- Protocol: **Cityscapes-day source → continual adaptation**, with evaluation "on the IDD-AW validation set".
- Per-class tables use the **19 Cityscapes classes**. This is not the 26-class IDD-AW protocol, so it is not comparable with supervised IDD-AW numbers.
- Model: DINOv2 + Mask2Former with VPT prompts and LoRA (r = 8 best).
- Reported numbers:
  - IDD-AW: IMP 58.4 vs SegBridge **69.8**.
  - C-Driving: 60.9 vs 68.7.
  - ACDC average: ViT-S 65.8, ViT-B 69.5, ViT-L 74.2.
  - The ACDC table also lists CoTTA, TENT, EATA and SAR with DeepLabv3+ / HGFormer sources.
- **Internal inconsistency:** the ablation table lists "All components (ours)" at **65.1** on IDD-AW, against 69.8 in the text. Ask which number is the reference, or rerun.
- **RGB only; NIR unused.**

**Scoop check.** An arXiv sweep for {test-time, source-free, domain adaptation} × {near-infrared, NIR} found only Pandey'20, the ECML-PKDD'26 synthetic-to-real NIR paper, and biometrics or fNIRS work — [arXiv API](https://export.arxiv.org/api/query?search_query=abs:(%22test-time%22+OR+%22source-free%22+OR+%22domain+adaptation%22)+AND+abs:(%22near-infrared%22+OR+NIR)+AND+cat:cs.CV&max_results=30).

### Inferences
- **What is open:** source-free or continual TTA where the RGB-trained model receives a **new, never-trained sensor at deployment**. The NIR view enters through either:
  - (a) "NIR as a near-red channel", input-level and fragile; or
  - (b) an aligned NIR encoder, as in ALT-1′.

  Reliability weighting then uses spectral physics or cross-view agreement.
- **Novelty:** A\* 5 (6 if merged with ALT-1′), Q1 7.
- **Feasibility:** high. TTA is cheap, and DINOv2-B with Mask2Former fits 24 GB at batch 1–2.
- **Crux risk:** how good is an RGB source model's prediction on raw NIR?
  - Pandey'20 needed generative latent search, which implies a real red→NIR gap.
  - In NIR, vegetation is bright and some painted or coloured surfaces lose contrast (mem physics).
  - Before committing, run a 1-day go/no-go: Cityscapes RGB model on IDD-AW NIR (3-channel replicate, and NIR in the red channel), reporting mIoU per condition.
- **Headline target:** Cityscapes → IDD-AW (19 classes) above SegBridge's 69.8 (or 65.1) using RGB+NIR at test time, with an RGB-only ablation, plus per-condition gains in low light and fog. This is plausible because a second real measurement is added, but unverified.
- **Main reviewer attack:** "Night-TTA / MM-TTA with NIR swapped in. The reliability weighting is AdaPGC- or READ-like." Defence: the source model never saw NIR, it is dense rather than classification, and it covers weather rather than night.
- **Colorization guardrail:** do not bridge by translating NIR→RGB (for example with HAQAGen) as the method. Use it only as a baseline ("colorize then segment"), which keeps the paper clearly non-colorization while using the group's expertise.

### Gaps
- SegBridge main-paper numbers and code were not accessed (403 via WebFetch; the supplement only via curl). The backbone behind 69.8 is unknown: ACDC ViT-B gives 69.5 and ViT-L 74.2.
- READ (ICLR'24) is cited from memory only.
- The Night-TTA full text, including whether it uses explicit cross-modal consistency losses, was not read.

---

## Q3. ALT-3: Label-efficient (1/5/10 %) RGB–NIR segmentation via cross-modal co-training

### Takeaway
Semi-supervised multimodal segmentation already has a benchmark (M3L, WACV'24) and RGB-T cross-modal pseudo-supervision (SpiderMesh). The RGB→new-modality supervision-transfer idea dates to 2016. An RGB-NIR version is a **dataset port**: about **3/10 A\***, **5/10 Q1**. It is not a stronger A\* idea than the current candidate.

### Cited Findings
- **M3L**, "Missing Modality Robustness in Semi-Supervised Multi-Modal Semantic Segmentation" (arXiv 2304.10756; WACV 2024 per wave1). It "create[s] the first benchmark for semi-supervised multi-modal semantic segmentation", with Linear Fusion plus a multimodal mean teacher with masked-modality learning. — [arXiv](https://arxiv.org/abs/2304.10756)
- **SpiderMesh** (arXiv 2303.08692). An RGB-T segmentation model that enables "semi-supervised learning to fully utilize RGB-T labels only sparsely available", using asymmetric M-CutOut and cross-modal pseudo supervision (MFNet, PST900). — [arXiv](https://arxiv.org/abs/2303.08692); described by a [web search summary](https://arxiv.org/pdf/2303.08692)
- **Cross Modal Distillation for Supervision Transfer** (arXiv 1507.00448): the same idea used as pre-training "for new modalities with limited labeled data". — [arXiv](https://arxiv.org/abs/1507.00448)
- **Label-efficient visible-infrared work exists in 2026**, e.g. "Aligned Consensus Teaching for Label-Efficient Oriented Object Detection in Weakly-Aligned Visible-Infrared Imagery" (arXiv 2609.18124; title only read). — [arXiv](https://arxiv.org/abs/2609.18124)
- An arXiv title sweep for semi-supervised / label-efficient × RGB-D / RGB-T / thermal / multimodal × segmentation returned mostly medical papers. There was **no RGB-NIR semi-supervised segmentation** paper. — [arXiv API](https://export.arxiv.org/api/query?search_query=ti:(semi-supervised+OR+semi+OR+%22label-efficient%22+OR+%22weakly%22)+AND+ti:(RGB-D+OR+RGB-T+OR+thermal+OR+multimodal+OR+multi-modal+OR+depth+OR+%22cross-modal%22+OR+infrared)+AND+ti:segmentation&max_results=40)
- The unlabeled RGB-NIR pools are domain-mismatched with IDD-AW (wave1 dataset audit):
  - FANVID: Chinese street scenes; masks are only Mask2Former pseudo-labels.
  - FMSVD: 240×320 video.
  - Kim & Baek: robot platform.

### Inferences
- **Novelty:** A\* 3, Q1 5. Strong RGB semi-supervised baselines with DINOv2 (UniMatch V2 class, mem) leave little headroom. "UniMatch + NIR" is an easy reject.
- **Feasibility:** high, and this is the cheapest option.
- **Best use:** as one table (1/8 or 1/16 labels) inside ALT-1′, showing that the label-free NIR plug-in also helps in the low-label regime. Not as a paper.

### Gaps
- No search of IEEE-only (non-arXiv) semi-supervised RGB-T papers from 2024–2026 (e.g., TCSVT, TIV). These probably exist and would lower novelty further.

---

## Q4. ALT-4: Other open non-colorization RGB–NIR gaps (max 2)

### Takeaway
- **Cross-spectral depth/stereo is taken.** Kim & Baek built a pixel-aligned RGB-NIR stereo system with fusion methods, and MS2 provides RGB/NIR/thermal stereo plus LiDAR in adverse conditions.
- **NIR road-surface wetness/ice is infeasible.** There is no public labeled RGB-NIR dataset, and silicon NIR misses the strong water bands.
- The two most defensible extra gaps:
  - **(4a) Cross-spectral disagreement as a label-free failure detector for safety-weighted segmentation** (IDD-AW Safe mIoU): about 5/10 A\*, prior art only partly searched.
  - **(4b) Thermal-privileged training with RGB-NIR deployment** on TRNT: about 4/10 A\*.

  Neither beats ALT-1′.

### Cited Findings
- **Kim & Baek, "Pixel-aligned RGB-NIR Stereo Imaging and Dataset for Robot Vision"** (arXiv 2411.18025; CVPR'25 per `paperB_novelty_audit.md`) — [arXiv](https://arxiv.org/abs/2411.18025).
  - Pixel-aligned RGB and NIR stereo plus LiDAR on a mobile robot, captured under "diverse lighting conditions".
  - Introduces "an RGB-NIR image fusion method and a feature fusion method" for downstream 3D tasks.
- **MS2 / extended MS2**, "Deep Depth Estimation from Thermal Image: Dataset, Benchmark, and Challenges" (arXiv 2503.22060) — [arXiv](https://arxiv.org/abs/2503.22060).
  - Targets depth "under adverse weather and lighting conditions".
  - Per `paperB_novelty_audit.md`: stereo RGB, NIR and thermal plus LiDAR, with semi-dense depth ground truth.
- **RASMD** (arXiv 2504.07603) is RGB + **SWIR** driving data for adverse conditions, not silicon NIR. — [arXiv](https://arxiv.org/abs/2504.07603)
- **Road-surface sweep:** NIR / multispectral × road-surface / black ice / wetness returned **no computer-vision dataset or method** in cs.CV (only materials-science hits). — [arXiv API](https://export.arxiv.org/api/query?search_query=abs:(%22road+surface%22+OR+%22road+condition%22+OR+%22black+ice%22+OR+wetness)+AND+abs:(%22near-infrared%22+OR+NIR+OR+multispectral+OR+%22infrared%22)&max_results=25)
- **Water absorption bands:** strong liquid-water and ice absorption lies near 1.45 and 1.94 µm (SWIR). Silicon NIR (≤ 1.0 µm) sees only a weak 970 nm feature. (mem; not re-verified)
- **TRNT** (AAAI 2026, arXiv 2602.06363): 8,281 pixel-aligned RGB-NIR-TIR triplets for pedestrian detection under uncertain modality availability. — [arXiv](https://arxiv.org/abs/2602.06363)
  - It already handles missing modalities, which overlaps with any "NIR instead of TIR" study.

### Inferences
**(4a) Cross-spectral disagreement for failure detection and selective prediction**
- Idea: the disagreement between a frozen RGB model's prediction and an aligned NIR student's prediction (from ALT-1′) is a free per-pixel error signal under weather. It can drive abstention or hierarchy back-off for Safe mIoU.
- Novelty: about 5 (A\*), 6–7 (Q1). I did not run a dedicated search for "multi-sensor disagreement failure prediction in segmentation".
- Best folded into ALT-1′ as an analysis section, not run as its own paper.

**(4b) Thermal-privileged training, RGB-NIR deployment**
- Idea: a cheap silicon sensor stack replaces LWIR at deployment.
- Novelty: about 4 (A\*). Learning with privileged information is old (wave1: Hoffman'16, HeatNet, CEKD), and TRNT already covers uncertain modalities.

**Depth / stereo:** 3–4 (A\*).

**Road wetness / ice:** not feasible with public data; do not pursue.

### Gaps
- The MS2 NIR illumination type and the per-condition NIR depth baselines were not read.
- I did not search for RGB-NIR video segmentation (no labeled RGB-NIR video set is known: FMSVD is unlabeled per wave1). I did not search for NIR + event (no public pixel-aligned RGB-NIR-event dataset known; mem).

---

## Q5. Is any alternative verifiably open at novelty ≥ 7 for A\*? Which most likely beats SOTA on public benchmarks? Ranking against the current candidate

### Takeaway
**No alternative is verifiably ≥ 7 on concept novelty.**
- **ALT-1′** is the only one with a credible path to 7. It combines a label-free NIR plug-in for frozen RGB foundation segmenters, reliability-masked cross-spectral distillation, test-time per-pixel fusion and optional TTA. It is evaluated on Cityscapes → IDD-AW (against SegBridge and Vireo), on supervised IDD-AW and on MCubeS.
- It is also the one most likely to post **better-than-published numbers on a public benchmark**: it adds a real second sensor to an RGB-only SOTA setting (SegBridge 69.8 on IDD-AW val; Vireo's OV-DGSS protocol).
- It scores higher than the current Paper A (A\* 5–6) and can reuse Paper A's physics descriptors as the reliability mask.

### Cited Findings
- **Current candidate (Paper A):** A\* 5/10 and Q1 7/10, because TIER-MoE (2607.27289), 2608.15261 and GAUGE (2608.05608) pre-empt cross-fitted / counterfactual gating — `paperA_novelty_audit.md` (local file), with sources therein, e.g. [TIER-MoE](https://arxiv.org/abs/2607.27289).
- **Published IDD-AW reference points:**
  - Supervised RGB+NIR: 68.32 mIoU (ICPR'24 #1); RGB-only Mask2Former: 68.54 — [ICPR'24 report](https://arxiv.org/abs/2409.05327) via `wave1_datasets_audit.md`.
  - Cityscapes→IDD-AW continual TTA (19 classes): SegBridge 69.8 (or 65.1 in its own ablation) — [SegBridge supplement](https://openaccess.thecvf.com/content/CVPR2026F/supplemental/Jangamreddy_Continual_Adaptation_of_CVPRF_2026_supplemental.pdf).
- **Vireo's OV-DGSS protocol** (Cityscapes/GTA5 → ACDC/BDD/Mapillary/DELIVER) has no NIR target, so IDD-AW is a clean extension — [Vireo](https://arxiv.org/html/2506.09881).
- **SpectraDINO's NIR result** (MCubeS 46.76) is weak relative to supervised fusion. That suggests naive DINOv2 distillation to NIR leaves headroom — [SpectraDINO](https://arxiv.org/html/2605.02258).

### Inferences
**Ranking**

| Rank | Idea | Novelty A\* / Q1 | Feasibility (MS student, 1×24 GB) | Most plausible headline | Main reviewer risk |
|---|---|---|---|---|---|
| 1 | **ALT-1′: label-free NIR plug-in for frozen RGB VFM segmenters (reliability-masked cross-spectral distillation + test-time fusion; OV and DG protocols)** | **6 (ceiling 7) / 8** | High. DINOv2-B LoRA student (~1–2 days per run); Mask2Former head frozen; Vireo baseline must be cut to fit 24 GB (paper used about 45 GB) | Cityscapes→IDD-AW above SegBridge (69.8) with zero NIR labels; largest gains in low light and fog; also improves zero-shot OV mIoU on IDD-AW over Talk2DINO / CAT-Seg / Vireo | "SpectraDINO + Talk2DINO + feature averaging". Needs the teacher-degradation analysis and a masked-vs-unmasked ablation to show a real insight. NIR-adds-little risk (2609.13265), so use sequence-disjoint splits. |
| 2 | Current Paper A (physics + counterfactual-gated supervised fusion) | 5–6 / 7 | High | Supervised IDD-AW above 68.3 / 68.5; MCubeS RGB-N about 53–54 | Pre-empted by TIER-MoE, 2608.15261 and GAUGE; gains about +1 mIoU |
| 3 | ALT-2 stand-alone (cross-spectral TTA) | 5 / 7 | High; low compute | Same Cityscapes→IDD-AW target | Night-TTA / MM-TTA port; AdaPGC (Nov 2026) holds the "reliability shift" framing; poor raw-NIR view |
| 4 | ALT-1 as literally stated (pure OV from NIR) | 5 / 7 | High | First OV / zero-shot numbers on IDD-AW NIR | OpenESS-for-NIR; weak "open-vocab" on a 26-class set |
| 5 | ALT-4a disagreement-as-failure-detector | about 5 / 6–7 (prior art not fully searched) | High | Better AUROC for error prediction and Safe mIoU under abstention | Seen as UQ, with NIR tangential |
| 6 | ALT-4b thermal-privileged RGB-NIR deployment (TRNT) | 4 / 6 | High | Closes X % of the RGB-NIR→RGB-NIR-TIR gap | LUPI is old; TRNT already does uncertain modality |
| 7 | ALT-3 semi-supervised RGB-NIR | 3 / 5 | Highest | +x mIoU at 1/16 labels | M3L / SpiderMesh port |
| 8 | Depth / stereo; road wetness | 3–4 / 5; n/a | Medium; infeasible | — | Kim & Baek / MS2 exist; no data |

**Suggested ALT-1′ minimum experiment set**
- **(E0) Go/no-go.** Run a Cityscapes DINOv2-B + Mask2Former model on IDD-AW RGB, NIR-replicated and NIR-in-red inputs, reporting per-condition mIoU.
- **(E1) Alignment.** Align a NIR student on unlabeled pairs (IDD-AW train images without labels, FANVID, MS2), comparing:
  - unmasked vs reliability-masked (entropy / clear-subset / physics) distillation;
  - the SpectraDINO recipe as a baseline.
- **(E2) Test-time fusion** (agreement-weighted vs mean vs oracle). Report Cityscapes→IDD-AW (19 classes) against SegBridge, CoTTA and a VFM source-only model.
- **(E3) OV protocol.** Run Talk2DINO / CAT-Seg / Vireo with and without the NIR plug-in on IDD-AW (26 classes, zero-shot) and MCubeS.
- **(E4) Upper bound.** Supervised IDD-AW fine-tuning, showing that the plug-in initialisation helps (ties to ALT-3).
- **(E5) Optional TTA stage** (ALT-2).
- **(E6) Colorize-then-segment baseline** (HAQAGen) to pre-empt "why not translate".

**Colorization distinction.** All alternatives work in feature or prediction space and never synthesise RGB from NIR, so they are clearly distinct from NIR→RGB colorization. The only overlap is the optional colorize-then-segment *baseline*, which strengthens the distinction.

### Gaps
- No experiment has been run, so every "expected headline" is an inference. The E0 go/no-go result determines whether ALT-2 and the test-time-fusion part of ALT-1′ are viable.
- The CVPR 2027 contemporaneity cutoff is Sept 15, 2026 (per `venues_figures_and_paperC.md`). SpectraDINO (May 2026) and Vireo (2025) are full prior art. FuseAdapt (Oct 2026) and AdaPGC-extended (Nov 2026) are contemporaneous for CVPR'27 but prior art for ICCV'27.
- No search of IEEE Xplore-only 2026 venues (T-ITS, TIV, RA-L) for "NIR + VFM alignment". A non-arXiv scoop is possible.
