# Paper B novelty and data audit: "Does NIR Make Low-Light Enhancement Useful for Machines?"

Audit date: 2026-11-03. This audit extends and corrects `wave1_nir_assisted_restoration.md` and `wave1_datasets_audit.md`; it does not redo them. The review stance is that of a hostile reviewer at a Q1 journal (Information Fusion, TCSVT, TIP, Pattern Recognition or TMM).

**Method.**
- Ran arXiv API sweeps on cs.CV for NIR / near-infrared / RGB-NIR × low-light / night × detection / segmentation / enhancement / task-driven / illuminant, covering 2018–Nov 2026.
- Pulled Semantic Scholar citation lists for DVN, FCENet, IDD-AW and Kim & Baek. The NAID and TRNT lists were rate-limited and never returned.
- Scanned CVF open-access titles for CVPR 2024/2025/2026, ICCV 2025 and WACV 2026.
- Ran a small number of web searches and read full text (arXiv HTML) of TRNT, Kim & Baek, NAID, FCENet, FD-INR, Niu MM'26, DVN, the ICPR'24 IDD-AW report, MS2-extended and AMFusion.
- Checked GitHub READMEs for AuNet, NAID, NVEU, DVN, IDDAW_kit and AutoNUE public-code.

ECCV 2026 proceedings were not yet on ECVA, so they were covered only through arXiv. "(mem)" marks an item cited from memory and not re-verified in this session.

## Q1. Verdict per Paper B contribution (novel / partially done / taken)

### Takeaway
No single paper does Paper B, but every component has a close neighbour.

| Contribution | Verdict |
|---|---|
| C1. The protocol study of NIR-guided vs RGB-only enhancers | **Partially done**. ICPR'24 ran DVN preprocessing on IDD-AW (P3-type); Kim & Baek CVPR'25 ran DVN and fusion under a frozen YOLO (P0-type) |
| C2. Stratification by photon budget and light source | **Novel** |
| C4. Illuminant-spectrum-aware gating | **Novel in restoration and perception**; the physics is old |
| C3/C5. "No-regret" gated residual trained through frozen task models | **Partially done or taken** at the component level. As written it has a logical hole: g=0 returns E_rgb(x), not x |

### Cited Findings

**C1. Study: P0–P4 × (RGB-only enhancers vs NIR-guided enhancers vs naive stacking), downstream segmentation and detection plus fidelity. Verdict: PARTIALLY DONE.**
- **ICPR 2024 IDD-AW competition, arXiv 2409.05327.**
  - The 3rd-place team (SemSeggers) used "Image Smoothing using RGB-NIR pairs through a pre-trained DarkVisionNet inference pipeline on IDD-AW, followed by fine-tuning a pre-trained Mask2Former", scoring 67.58 mIoU.
  - The 1st-place team fused RGB and NIR "using the FusionNet method for the data preprocessing" and scored 68.32 mIoU.
  - Both are NIR-guided enhancement/fusion judged by real-data segmentation, P3-style (enhanced→enhanced), aggregated over all conditions, with no RGB-only-enhancer control and no protocol comparison. — [arXiv 2409.05327](https://arxiv.org/abs/2409.05327)
- **Kim & Baek, "Pixel-aligned RGB-NIR Stereo Imaging and Dataset for Robot Vision", CVPR 2025 (arXiv 2411.18025).**
  - The fused image feeds a pretrained YOLO without fine-tuning, i.e. P0-like.
  - Their Table 1 includes **DarkVision (DVN) as a baseline**. Detection mAP: RGB 0.756, NIR 0.703, DVN 0.762, Adaptive 0.773, theirs 0.809.
  - So a NIR-guided restorer *has* already been scored by a frozen detector on real data. The data are actively lit (an "Advanced Illumination AL295-150850IC" NIR source, i.e. 850 nm), there is one detector, and there is no protocol comparison.
  - Source: [arXiv 2411.18025](https://arxiv.org/html/2411.18025v2); CVPR 2025 confirmed on [CVF](https://openaccess.thecvf.com/content/CVPR2025/html/Kim_Pixel-aligned_RGB-NIR_Stereo_Imaging_and_Dataset_for_Robot_Vision_CVPR_2025_paper.html)
- **Kim & Baek's fusion training.**
  - The fusion network is trained "using photometric loss and stereo depth reconstruction loss, without training the downstream vision models".
  - This is NIR fusion trained through a frozen downstream model, but for stereo depth, not detection. — [arXiv 2411.18025](https://arxiv.org/html/2411.18025v2)
- **IDD-AW, WACV 2024.**
  - Uses naive early stacking with InternImage-b. mIoU for RGB / NIR / stack: low light 62 / 57 / 63; all conditions 64 / 61 / 67.
  - This is the stacking baseline Paper B needs; it already exists, per condition. — [arXiv 2311.14459](https://arxiv.org/abs/2311.14459); local text at `/tmp/claude-0/-home-user/62eeef81-e163-5d0c-ab02-e8bd308466a5/scratchpad/dl/iddaw.txt`
- **The NIR-guided restorers themselves report no downstream metric.**
  - NAID/SFM (TMM 2025), FCENet (Wang et al., CVPR 2025) and Niu et al. 3DarkFusion (ACM MM 2026) report fidelity metrics only. The full-text grep found no detection, segmentation or YOLO evaluation.
  - FD-INR (arXiv 2604.16800) says in its introduction that it is "significantly improving the robustness of downstream tasks such as object detection", but reports no detection result.
  - Sources: [2404.08514](https://arxiv.org/html/2404.08514v4), [2412.16645](https://arxiv.org/html/2412.16645v2) ([CVF CVPR 2025](https://openaccess.thecvf.com/CVPR2025?day=all)), [2607.29684](https://arxiv.org/html/2607.29684), [2604.16800](https://arxiv.org/html/2604.16800)
- **Low-tier precedent: Deng, Pan, Jin, Xie, "Night Target Detection Approach Based on Near Infrared Image Fusion on Vehicles", PRAI 2022** (DOI 10.1109/PRAI55851.2022.9904229).
  - Pixel-level VIS+NIR fusion followed by YOLOv4.
  - A search snippet says dark-night accuracy went from 20.0% to 82.4%. Full text was not read (IEEE blocked access): **unverified**. — [Crossref/IEEE](https://ieeexplore.ieee.org/document/9904229/)
- **RGB-only evidence is already published.** That restoration or enhancement can lower downstream accuracy appears in Sun et al. NeurIPS 2022, Wu et al. [arXiv 2409.14461](https://arxiv.org/abs/2409.14461), LIME-Eval [2410.08810](https://arxiv.org/abs/2410.08810) and Cheng et al. CVPRW 2026 (LoViF). This is per the thesis's own full-text audit in `/home/user/lowlight/research/literature/06_paper1_study_novelty.md`.

**C2. Photon-budget stratification (active vs passive NIR; HPS/MH vs halogen vs white LED). Verdict: NOVEL as an analysis axis.**
- No paper found stratifies NIR-guided restoration or perception by illuminant spectrum. The sweeps covered NIR × street light, sodium, light source, illuminant and night in cs.CV.
- The closest is Kim & Baek's image model, which separates ambient E_i^c from an active term L_i^c that is "present only in the NIR channel". It does not use source type. — [arXiv 2411.18025](https://arxiv.org/html/2411.18025v2)
- Every paired NIR-guided low-light set uses active NIR:
  - Real-NAID: a "built-in NIR illuminator … 750 nm and 1100 nm" ([2404.08514](https://arxiv.org/html/2404.08514v4)).
  - Kim & Baek: 850 nm source.
  - Niu MM'26: "An 850 nm NIR LED" ([2607.29684](https://arxiv.org/html/2607.29684)).
  - FMSVD: 850 nm LEDs, per the wave-1 audit.

**C3. No-regret residual y = E_rgb(x) + g ⊙ R(x, n), where g=0 recovers RGB-only. Verdict: PARTIALLY DONE (components taken; the explicit relative-to-input hinge is a thin gap).**
- **Gated or selective NIR injection already exists:**
  - SFM's global/local modulation weights (NAID, TMM 2025).
  - DVN's Deep Inconsistency Prior (AAAI 2022).
  - Kim & Baek's spatially varying α, β weights.
  - AUNet's uncertainty-aware router, which outputs a binary availability per modality, including NIR (TRNT, AAAI 2026).
  - Sources: [2404.08514](https://arxiv.org/html/2404.08514v4), [2411.18025](https://arxiv.org/html/2411.18025v2), [2602.06363](https://arxiv.org/html/2602.06363)
- **Bijelic et al., "Seeing Through Fog Without Seeing Fog", CVPR 2020 (arXiv 1902.08913).** "adaptively fuses features, driven by measurement entropy" across camera, lidar, radar and **gated NIR**, for detection in adverse weather. This is reliability-gated NIR fusion for detection on real data. — [arXiv 1902.08913](https://arxiv.org/abs/1902.08913)
- **The thesis already rated an RGB-only "no-regret enhancement" at 3/10 and retired it.**
  - Its precedents: TaskGuard (arXiv 2609.08011) per-region counterfactual harm map; Sun 2022 Eq. 5 detection-loss constraint; DBDiff (CVPRW 2026) fusion map; FoCo; AMIEOD's per-image argmin.
  - Its conclusion: "The only thin gap: no low-light paper imposes an explicit *relative-to-raw* constraint."
  - Source: `/home/user/lowlight/research/literature/06_paper1_study_novelty.md` §4; `/home/user/lowlight/research/PUBLICATION_ROADMAP.md` §0.

**C4. Illuminant-aware NIR reliability estimator (NIR/RGB ratio statistics, NIR noise). Verdict: NOVEL for gating restoration or perception; the underlying cue is OLD.**
- **Fredembach & Süsstrunk, "Illuminant estimation and detection using near-infrared", SPIE EI 2009, vol. 7250** (DOI 10.1117/12.806431). Illuminants are estimated from RGB/NIR image ratios, which also "pinpoint the location of diverse illuminant and recover a lighting map". — [EPFL Infoscience](https://infoscience.epfl.ch/record/133341)
- **Han, Ha & Kim, "Spatio-Spectral Deep Color Constancy With Multi-Band NIR", IEEE Access 12:105651–105661, 2024** (DOI 10.1109/ACCESS.2024.3434574). It has local-illuminant and confidence subnetworks and a 604-image hyperspectral dataset. — [Korea Univ. record](https://pure.korea.ac.kr/en/publications/spatio-spectral-deep-color-constancy-with-multi-band-nir/)
- **"Spectrum Aware Illumination Estimation Using Multispectral Image", IEEE TCSVT 2026** (arXiv 2606.14248, DOI 10.1109/TCSVT.2026.3701975). Estimates the illuminant spectrum from multispectral images; it does not gate restoration. — [arXiv 2606.14248](https://arxiv.org/abs/2606.14248)
- **Illumination-*level*-aware gating is common in RGB-thermal fusion.**
  - TRNT's related work cites "Illumination-aware Faster R-CNN … dynamically weights RGB and TIR sub-networks via illumination-based gating". — [2602.06363](https://arxiv.org/html/2602.06363)
  - IAF-Net (arXiv 2605.30939) adapts fusion weights to illumination, but for RGB + geometry. — [2605.30939](https://arxiv.org/abs/2605.30939)
  - PIAFusion (Information Fusion 2022) (mem).
  - Consequence: the word "illuminant-aware" will be read as this unless Paper B stresses *spectrum / source type* rather than brightness.

**C5. Training through frozen downstream models (task loss + self-supervised exposure/noise loss + per-image regret hinge). Verdict: TAKEN for RGB-thermal and RGB-only; new only in combination with NIR guidance and a relative hinge.**
- TDFusion, CVPR 2025 (arXiv 2412.03240): "a learnable fusion loss guided by task loss … TDFusion's training relies entirely on the downstream task loss". — [arXiv 2412.03240](https://arxiv.org/abs/2412.03240)
- **CVPR 2026 task-aware infrared-visible works (thermal), from CVF titles:**
  - Yang et al., "Customized Fusion: A Closed-Loop Dynamic Network for Adaptive Multi-Task-Aware Infrared-Visible …"
  - Ma et al., "Taming Generative Diffusion Model for Task-Oriented Infrared Imaging"
  - Source: [CVF CVPR 2026 list](https://openaccess.thecvf.com/CVPR2026?day=all)
- AMFusion (arXiv 2403.01083) uses a "Detection-guided Semantic Fusion Module" with a pre-trained YOLOv5s for **night** fusion, but it is **thermal** (M3FD + LLVIP). — [arXiv 2403.01083](https://arxiv.org/abs/2403.01083)
- TarDAL (CVPR 2022, arXiv 2203.16220), SeAFusion (Information Fusion 2022), CDDFuse (CVPR 2023, arXiv 2211.14461) are thermal task-driven or "lossless" fusion (mem; venues listed in the wave-1 notes).

### Inferences
- **The hole in the "no-regret" design is serious.** With g=0, y = E_rgb(x). The thesis showed E_rgb(x) usually *hurts* raw-trained detectors (P2). So falling back to the RGB-only path is **not** falling back to "no regret" relative to x.
  - The regret hinge max(0, L_task(y) − L_task(x)) compares against raw x, yet the architecture cannot output x unless E_rgb can also be bypassed.
  - Fix: y = x + a ⊙ (E_rgb(x) − x) + g ⊙ R(x, n), with two gates, so that (a, g) = (0, 0) is the identity. Alternatively, define regret against max-utility(x, E_rgb(x)).
  - A reviewer will also point out that a training-time hinge is not a guarantee. Call it "non-inferiority-regularised", and report the measured regret rate on held-out models.
- **"No-regret" collides with online-learning terminology** (sublinear regret). The thesis notes already proposed "non-inferiority-constrained enhancement" with a primal-dual solver; reuse that wording.
- **C1's sharpest defensible novelty** is the *cross-protocol* (P0/P2/P3/P4) and *cross-family* (RGB-only vs NIR-guided vs stacking) comparison under identical budgets, plus stratification by photon budget. Neither ICPR'24 (P3 only) nor Kim & Baek (P0 only, active NIR) does this.

### Gaps
- Kim & Baek's detection labels: the main text does not say where the YOLO ground truth came from. It may be in the 32-page supplement, which was not read.
- FusionNet as used in ICPR'24: which paper this "FusionNet" is was not resolved.
- TaskGuard, DBDiff, FoCo and AMIEOD details are taken from the thesis's own full-text notes and were not re-read here.

## Q2. Has any NIR-guided enhancement/denoising method been evaluated by downstream detection/segmentation on real night data, or trained with a task loss? (2025–Nov 2026 sweep)

### Takeaway
Yes, partially. Three things must be acknowledged:
1. Kim & Baek (CVPR'25) evaluated DVN and their own learned RGB-NIR fusion with a frozen YOLO on real, actively lit data, and trained the fusion through a frozen stereo network.
2. ICPR'24 entrants ran DVN preprocessing before IDD-AW segmentation.
3. TRNT (AAAI'26) and STF (CVPR'20) do NIR fusion for detection *inside the detector*.

No paper found trains a **NIR-guided enhancer** with a **detection or segmentation loss**, evaluates **passive** night NIR, or runs **P0–P4**.

### Cited Findings
- **arXiv sweep, cs.CV, abstracts with NIR or near-infrared × low-light / night / nighttime × detection / segmentation / downstream**: 10 total hits. The relevant ones are IDD-AW, ICPR'24, TRNT, SAMFusion, SpectraDINO and AgriNight. None is an NIR-guided *enhancer* evaluated downstream. — [arXiv API query](https://export.arxiv.org/api/query?search_query=cat:cs.CV+AND+(abs:NIR+OR+abs:%22near-infrared%22)+AND+(abs:night+OR+abs:%22low-light%22)+AND+(abs:detection+OR+abs:segmentation))
- **NIR + (task-driven | task-oriented | detection-driven | task loss | high-level vision)**: no relevant computer-vision hit. — [arXiv API](https://export.arxiv.org/api/query?search_query=(abs:NIR+OR+abs:%22near-infrared%22)+AND+(abs:%22task-driven%22+OR+abs:%22task+loss%22))
- **Papers citing DVN in 2025–26** (Semantic Scholar): FD-INR; Niu MM'26; NIRGB-GS (*Advanced Intelligent Systems* 2026, Gaussian-splatting reconstruction); NIRFuseNet (ISCAIT 2026, scene classification); "A Patch-Based CNN Approach for Object Detection Using RGB and NIR Image Fusion" (ICARES 2025, low tier, not read); "Frequency-aware wavelet fusion network for visible and near-infrared images" (*Infrared Physics & Technology* 2026). None is a Q1/A* NIR-guided enhancer with downstream evaluation. — [Semantic Scholar API](https://api.semanticscholar.org/graph/v1/paper/arXiv:2303.06834/citations)
- **Papers citing FCENet** (15): none is a NIR-guided enhancer with downstream evaluation. — [Semantic Scholar API](https://api.semanticscholar.org/graph/v1/paper/arXiv:2412.16645/citations)
- **Papers citing IDD-AW** (29), relevant ones:
  - UGF-Net (WcCST 2026), "Uncertainty-Guided Fusion for Robust RGB-NIR Traffic Semantic Segmentation in Adverse Weather"
  - "Color Space Fusion for Semantic Segmentation in Indian Adverse Weather Driving Scenes" (ICNTE 2026)
  - "Texture-Shape Bias Balancing … Automotive NIR Imagery" (ECML PKDD 2026, arXiv 2606.15072; NIR-only segmentation)
  - None is Q1/A*. — [Semantic Scholar API](https://api.semanticscholar.org/graph/v1/paper/arXiv:2311.14459/citations)
- **TRNT/AUNet, AAAI 2026** (Proc. AAAI 40(4):2462–2470). AP50: RGB 77.0, NIR 72.6, RGB+NIR 91.6 (wave-1 numbers). This is fusion inside the detector, not enhancement. — [AAAI OJS](https://ojs.aaai.org/index.php/AAAI/article/view/37232); [arXiv 2602.06363](https://arxiv.org/html/2602.06363)
- **CLEAR-IR, IROS 2026** (arXiv 2510.04883). Reconstructs "clean, RGB styled images from emitter populated IR inputs … enabling standard RGB trained vision pipelines to operate without visible light", and is evaluated on object detection, ArUco and SLAM. Active IR; IR-only → RGB-style. This is a P0-style precedent close to colorization. — [arXiv 2510.04883](https://arxiv.org/abs/2510.04883)
- **SpectraDINO** (arXiv 2605.02258, May 2026) states "no annotated SWIR segmentation and NIR detection benchmarks are publicly available", so it evaluates NIR on MCubeS segmentation only. That statement overlooks TRNT. — [arXiv 2605.02258](https://arxiv.org/html/2605.02258)
- **Negative-result precedent on NIR:** "Forward-Facing Near-Infrared Adds Little to Colour for Farm-Machinery Traversability" (arXiv 2609.13265, Sep 2026). Once spatial leakage is removed, NIR or fusion "does not consistently outperform standard color". Daytime, agriculture. Reviewers may use it to argue NIR gains are leakage artefacts. — [arXiv 2609.13265](https://arxiv.org/abs/2609.13265)
- **CVF title scan** (CVPR 2024/2025/2026, ICCV 2025, WACV 2026): the only NIR-guided restoration title is FCENet (CVPR 2025). No CVPR 2026 title combines NIR guidance with low-light enhancement and detection or segmentation. — [CVF CVPR 2026](https://openaccess.thecvf.com/CVPR2026?day=all), [CVPR 2025](https://openaccess.thecvf.com/CVPR2025?day=all)
- **Web search** for 2026 NIR-guided low-light enhancement plus detection found no such paper; the closest is Niu MM'26, which reports no detection. — [arXiv 2607.29684](https://arxiv.org/abs/2607.29684)

### Inferences
- Correct the wave-1 sentence "No NIR-guided restorer has been evaluated by downstream detection or segmentation on real night data" to:

  > NIR-guided restorers have been scored downstream only incidentally: DVN as a frozen-YOLO baseline on actively lit robot data (Kim & Baek, CVPR 2025), and DVN or FusionNet preprocessing in IDD-AW competition entries (ICPR 2024, P3-type, aggregate). None was trained with a task loss, compared against RGB-only enhancers, run across protocols, or analysed by NIR photon budget.

- Including SFM, FCENet and FD-INR is new as *downstream* evaluation, but reviewers will note they were trained on Real-NAID or DVD (active NIR, static) and are being tested off-domain. That is a domain-shift confound; a fine-tuned arm is needed.

### Gaps
- Not covered: IEEE Xplore (TIP, TCSVT, TMM, TITS) and Information Fusion / Pattern Recognition articles without an arXiv version. Searches were blocked: IEEE returned HTTP 418 or empty pages, and Semantic Scholar keyword search returned 429 throughout. A manual Scopus or IEEE Xplore query is still needed for "RGB-NIR" AND ("low-light" OR night) AND (detection OR segmentation), 2025–2026.
- Semantic Scholar citation lists for NAID (2404.08514) and TRNT (2602.06363) could not be retrieved (rate-limited).
- NeurIPS 2026 and ECCV 2026 proceedings were not checked directly.

## Q3. Do task-driven fusion / "no-regret" / fallback / identity-preserving methods already give a never-worse-than-input guarantee for NIR guidance?

### Takeaway
None found gives a never-worse-than-input guarantee for NIR guidance. Nor does any RGB-only low-light method beyond soft constraints:
- Sun 2022's detection-loss constraint is absolute, not relative to the input.
- TaskGuard's and AMIEOD's selectors are binary accept/reject per image or region.

The gap is genuine but thin. It is defensible only as a measured, held-out-model non-inferiority (regret-rate) result, not as a "guarantee".

### Cited Findings
- **Turgeman & Tirer, "Does the Data Processing Inequality Reflect Practice? On the Utility of Low-Level Tasks", ICLR 2026** (arXiv 2512.21315). They "prove that for any finite number of training samples, there exists a pre-classification processing that improves the classification accuracy". This is the theoretical counterweight to "enhancement adds no information". — [arXiv 2512.21315](https://arxiv.org/abs/2512.21315)
- **TDFusion, CVPR 2025**: meta-learned fusion loss driven by the task loss. No identity or regret constraint is described in the abstract. — [arXiv 2412.03240](https://arxiv.org/abs/2412.03240)
- **TaskGuard** (arXiv 2609.08011):
  - Per-image accept/reject of a restoration, using regional counterfactuals B_r = L_det(I_d) − L_det(I_d + M_r ⊙ R).
  - 51.7% of regions helped and 48.3% hurt.
  - No low light; frozen YOLO11s / RT-DETR; COCO and DAWN.
  - Source: thesis full-text notes, `/home/user/lowlight/research/literature/05_paper2_distillation_novelty_and_design.md` and `06_paper1_study_novelty.md`. Not re-read here.
- **Sun et al., NeurIPS 2022**: an Eq. 5 constraint on detection loss (absolute bound); YOLOv3 trained on VOC and tested on ExDark: raw 46.27, SCI 48.47, ZeroDCE 41.01. — thesis notes `06_paper1_study_novelty.md`; [NeurIPS 2022 poster](https://neurips.cc/virtual/2022/poster/54700)
- **WSA-YOLO** is described elsewhere as "a weakly supervised adaptive low-light object detector based on YOLOv7 … decomposes the image into reflectance and illumination maps". Nothing found supports the "invertible" label used in wave 1, and its venue remains unverified. — [MDPI Electronics 15(10):2057 citing it](https://www.mdpi.com/2079-9292/15/10/2057)
- **AUNet's uncertainty-aware router** gives binary availability v ∈ {0,1}³ for RGB, NIR and TIR. This is a hard modality fallback, but it is trained against *availability*, not task regret. — [arXiv 2602.06363](https://arxiv.org/html/2602.06363)
- **STF's entropy-driven adaptive fusion** with gated NIR: a reliability gate, not a no-regret guarantee. — [arXiv 1902.08913](https://arxiv.org/abs/1902.08913)
- **The thesis roadmap retired** the standalone no-regret enhancer (3/10) and moved the **regret-rate metric** into Paper 1. — `/home/user/lowlight/research/PUBLICATION_ROADMAP.md`

### Inferences
- To beat this objection, the relative-to-input constraint must be:
  - (i) solved as a constraint (primal-dual or Lagrangian with ε), not a fixed-weight hinge;
  - (ii) evaluated as regret rate on **held-out** frozen models and datasets not used in training;
  - (iii) built so the identity is architecturally reachable (see the Q1 fix).
- Without (ii), a reviewer will say the hinge only overfits the training detector.
- The one NIR-specific angle no one has: the **constraint plus NIR as a real measurement** lets the method sometimes strictly *gain* where RGB-only non-inferiority can at best tie. Show this as a Pareto front of regret rate vs mean ΔmIoU / ΔAP.

### Gaps
- TarDAL, SeAFusion and CDDFuse were not re-read; whether any states an explicit input-relative bound is from memory (none does, to my knowledge).

## Q4. Illuminant-spectrum-aware NIR reliability: does anyone use light-source type to gate NIR guidance? Do white LEDs emit negligible 740–1000 nm?

### Takeaway
No restoration or perception method found uses light-source type or spectrum to gate NIR guidance; the cue exists only in colour-constancy work (2009, 2024, 2026).

A measured source confirms that white LED lamps have "virtually no emission beyond 800 nm". Halogen and incandescent sources peak in the NIR, and HPS and metal-halide lamps have strong 819 nm lines. The 740–800 nm sliver of IDD-AW's NIR band is not covered by that statement.

### Cited Findings
- **Elvidge et al., "Spectral Identification of Lighting Type and Character", *Sensors* 2010.**
  - 43 lamps measured with an ASD spectrometer (VNIR 350–1000 nm at 3 nm FWHM, out to 2500 nm), including 13 LED lamps (white LED streetlights, cool and warm).
  - "The LED spectra are typified by relatively narrow emission bands and virtually no NIR emission … The measured LED lamps had virtually no emission beyond 800 nm."
  - Metal halide: "Each of the metal halide lamps had a strong emission at 819 nm."
  - Sodium: "The strongest emission line is from the set of sodium emissions at 819 nm."
  - Thermal sources: "Lamps that produce light through heat (incandescent, quartz halogens, and fuel lamps) emit primarily like blackbodies, with peak emission in the near infrared."
  - Source: [PMC3274255](https://pmc.ncbi.nlm.nih.gov/articles/PMC3274255/)
- **Light-source context for IDD-AW (India):** EESL's Street Lighting National Programme has installed over 1.03 crore (about 10.3 million) smart LED street lights, and Parliament figures of about 1.25–1.31 crore are cited. — [Millennium Post](https://www.millenniumpost.in/business/eesl-installs-over-103-cr-smart-led-street-lights-in-country-so-far-394458); [pv magazine India 2019](https://www.pv-magazine-india.com/2019/10/02/india-replaces-one-crore-conventional-streetlights-with-energy-efficient-leds)
- **Colour-constancy precedents:**
  - Fredembach & Süsstrunk 2009, NIR/RGB ratio illuminant detection and lighting maps. — [EPFL](https://infoscience.epfl.ch/record/133341)
  - Han et al., IEEE Access 2024, multi-band NIR colour constancy with a confidence subnetwork. — [Korea Univ.](https://pure.korea.ac.kr/en/publications/spatio-spectral-deep-color-constancy-with-multi-band-nir/)
  - TCSVT 2026, spectrum-aware illuminant estimation (arXiv 2606.14248). — [arXiv](https://arxiv.org/abs/2606.14248)
- **Illumination-spectrum design for NIR-assisted seeing in the dark:** "Visibility Constrained Wide-band Illumination Spectrum Design for Seeing-in-the-Dark", CVPR 2023 (arXiv 2303.11642). It designs *active* VIS-NIR LED multiplexing for NIR→RGB; it does not gate by ambient source. — [arXiv 2303.11642](https://arxiv.org/abs/2303.11642)
- **Physics-based NIR attack:** "Physics-Based Adversarial Attack on Near-Infrared Human Detector for Nighttime Surveillance Camera Systems" (arXiv 2412.13709) shows that NIR detectors under active IR depend on material NIR reflectance. Relevant to the reliability argument. — [arXiv 2412.13709](https://arxiv.org/abs/2412.13709)
- **arXiv sweep** (street light / sodium / light source / illuminant × NIR, cs.CV, 2018–2026): 51 hits, none gating restoration or perception by source type. — [arXiv API](https://export.arxiv.org/api/query?search_query=(abs:%22street+light%22+OR+abs:sodium+OR+abs:%22light+source%22+OR+abs:illuminant)+AND+(abs:NIR+OR+abs:%22near-infrared%22)+AND+cat:cs.CV)

### Inferences
- **Physical prediction Paper B can test.** In IDD-AW low light, passive NIR SNR should be high near halogen headlights and sodium or metal-halide lamps, and low under white-LED street lighting and LED headlights. Gains from NIR guidance should track the NIR/RGB ratio.
- **Why the prediction matters.** IDD-AW's low-light stacking gain is only +1 mIoU (62 → 63). If India's urban lighting is mostly LED, passive NIR may carry little signal, and a null result is a real risk. Pre-register it as a hypothesis, not as a selling point.
- **"Negligible NIR from LEDs" needs a caveat.** Elvidge's claim is for >800 nm. Warm-white or high-CRI phosphors can tail into 700–780 nm, and IDD-AW's NIR band starts at 740 nm. Phrase it as "little emission in 740–1000 nm; virtually none beyond 800 nm [Elvidge 2010]".

### Gaps
- No modern (2020s) measured spectra of LED automotive headlights or current Indian LED streetlights were retrieved.
- IDD-AW provides no light-source labels, so Paper B must annotate them, for example on the 1000 low-light images, by NIR/RGB ratio clustering plus manual checks.

## Q5. Data availability facts (a)–(d)

### Takeaway

| Item | Status |
|---|---|
| (a) IDD-AW NIR illumination | Most likely passive, but **unverified**. Neither the 10-page WACV paper nor the project page mentions any illuminator |
| (b) IDD-AW polygon JSONs with instances | Very likely, since the official kit points to the IDD/AutoNUE polygon-JSON pipeline and the page says "instance segmentation". **Confirm on download** |
| (c) TRNT | **Public** (Baidu, CC BY 4.0), with TXT pedestrian boxes. NIR band, active/passive status and night-subset size are **not stated** |
| (d) Real-NAID, DVD, FMSVD | All have download links |

Other RGB-NIR night sets with labels:
- STF (gated NIR, boxes)
- AgriNight (unpaired NIR night, masks)
- MS2 (depth only)
- Kim & Baek (depth; labels not stated)

### Cited Findings

**(a) IDD-AW illumination.**
- The paper says: "we used JAI's FS-3200D-10GE camera. This is a 2-CMOS multispectral prism camera … a visible color channel from 400–670 nm and a near-infrared (NIR) channel from 740–1000 nm". The 10-page PDF has no supplement and no illuminator mention. — local `iddaw.txt` lines 176–180; [arXiv 2311.14459](https://arxiv.org/abs/2311.14459)
- The project page mentions "high-resolution RGB and NIR camera sensors" and nothing about active, IR-lamp or passive illumination. — [iddaw.github.io](https://iddaw.github.io/)

**(b) IDD-AW labels.**
- The project page says the data are "annotated for semantic and instance segmentation" with a "4-level label hierarchy". — [iddaw.github.io](https://iddaw.github.io/)
- The official IDDAW_kit README says: "For mask generation, we follow the same instructions as specified in … https://github.com/AutoNUE/public-code. However, just replace createLabels_iddaw.py in place of createLabels.py". — [Furqan7007/IDDAW_kit](https://github.com/Furqan7007/IDDAW_kit)
- AutoNUE's `createLabels.py` globs `*_gt*_polygons.json`. With `--instance True` it calls `json2instanceImg`, writing `*_instance{id}s.png`; it also supports panoptic output. — [AutoNUE public-code](https://github.com/AutoNUE/public-code)
- The kit's own file lists reference only `…/leftImg8bit/…/00000000_rgb.png` and generated `…/level3Ids/…_labellevel3Ids.png`. Its `train.txt` has 3926 lines and `val.txt` 1072 (an internal split). — [IDDAW_kit/iddaw](https://github.com/Furqan7007/IDDAW_kit/tree/main/iddaw)
- **Data access:** register on the IDD portal and get a 24 h token. **Splits:** the public ICPR split is 3430 / 475 / 1095; low light is 684 / 100 / 216; test labels were held back. — [ICPR'24 report](https://arxiv.org/abs/2409.05327)

**(c) TRNT / AUNet (AAAI 2026).**
- **Size and split:** 8,281 pixel-aligned triplets: 7,429 ground-view (PTZ) and 852 UAV (Matrice 350 RTK). 640×480; train 7,026 / test 1,255, following CVC-14; every image has at least one pedestrian.
- **Capture time and light:** 08:00–22:00, including "extremely low illumination (dark environments without urban/car lights)"; three seasons.
- **Not stated in the paper:** the NIR band, the camera model, whether NIR is actively illuminated, or a night-subset count. The paper speaks only generically of NIR texture "particularly under active nighttime illumination". — [arXiv 2602.06363](https://arxiv.org/html/2602.06363)
- **Release:** the GitHub README publishes the dataset on Baidu (`pan.baidu.com/s/1IleD45JYPDKLYdrtCYLHuw`, code `drnt`), with folders RGB/NIR/TIR/label and train/test splits, annotations in "TXT format", license "CC BY 4.0". It does not document day/night tags. — [medicineqian/AuNet](https://github.com/medicineqian/AuNet)
- **Venue:** Proc. AAAI 40(4):2462–2470. — [AAAI OJS 37232](https://ojs.aaai.org/index.php/AAAI/article/view/37232)

**(d) Restoration sets.**
- **Real-NAID**: Baidu Netdisk link, password `ik9u`. — [ronjonxu/NAID](https://github.com/ronjonxu/NAID)
  - 100 scenes × ISO 4000 / 12000 / 32000, clean reference at ISO 600.
  - Huawei X2381-VG camera "equipped with a built-in NIR illuminator … between 750 nm and 1100 nm".
  - Images cropped to 2048×2160; 70/30 split.
  - Source for the capture details: [arXiv 2404.08514](https://arxiv.org/html/2404.08514v4)
- **DVD**: the DVN README links a Google Drive folder with `DVD_train_raw`, `DVD_test`, `DVD_real`; it is Apache-2.0, and the clone line is still a placeholder. — [megvii-research/DVN](https://github.com/megvii-research/DVN)
  - DVN paper: pairs captured "by switching an optical filter placed directly in front of the camera without an IR-Cut"; 10 real noisy 1920×1080 pairs for testing.
  - NAID: DVD "captures images within a controlled light-box environment", and its noise is synthesised.
  - Sources: [DVN HTML](https://arxiv.org/html/2303.06834), [NAID](https://arxiv.org/html/2404.08514v4)
  - FCENet and NAID both trained on DVD, so it is obtainable in practice.
- **FMSVD**: Google Drive links, split or single zip; no labels. — [MyNiuuu/NVEU](https://github.com/MyNiuuu/NVEU)

**Other RGB-NIR night sets.**
- **Seeing Through Fog (STF/DENSE), CVPR 2020**: "100k labels for lidar, camera, radar, and gated NIR sensors". Gated NIR is active; the cameras are separate, not pixel-aligned. Public on GitHub. — [arXiv 1902.08913](https://arxiv.org/abs/1902.08913)
- **AgriNight, IROS 2026** (arXiv 2607.12065): 428 day RGB and 549 night images from "night-vision-equipped mobile robots", with pixel-wise labels. Day RGB and night NIR are **unpaired**, and there is a visibility mask for the limited NIR illumination range. — [arXiv 2607.12065](https://arxiv.org/abs/2607.12065)
- **MS2 / extended MS2** (arXiv 2503.22060): stereo RGB, NIR, thermal and LiDAR, with semi-dense depth ground truth only; illumination of the NIR not stated in the extracted text. — [arXiv 2503.22060](https://arxiv.org/html/2503.22060)
- **Kim & Baek**: pixel-aligned RGB-NIR stereo, active 850 nm, LiDAR depth. The paper says "we collect and share a dataset"; detection labels are not described in the main text. — [arXiv 2411.18025](https://arxiv.org/html/2411.18025v2)
- **3L-D** (arXiv 2611.02695, 2 Nov 2026): a calibrated low-light dataset reproducing night levels 1–5 "spanning the visible to near-infrared spectrum". The abstract does not indicate a separate NIR channel or task labels. — [arXiv 2611.02695](https://arxiv.org/abs/2611.02695)

### Inferences
- **Detection on IDD-AW.**
  - If the download contains per-object polygons, as the AutoNUE pipeline implies, deriving boxes is straightforward for traffic participants: person, rider, car, truck, bus, motorcycle, bicycle, autorickshaw, animal.
  - Write the converter so it falls back to connected components on `level3Ids` if instance JSONs are absent; this degrades crowded scenes.
  - "Color Space Fusion …" (ICNTE 2026) reportedly reports mAP50 on IDD-AW (wave-1 audit, unverified). Check it before claiming "first detection on IDD-AW".
- **TRNT** is the only public pixel-aligned RGB-NIR set with night boxes, but it is pedestrian-only, 640×480 and a surveillance view.
  - The night subset must be built by the student, from timestamps if filenames carry them or by RGB brightness thresholding.
  - Whether its NIR is active is unknown. PTZ surveillance cameras typically have built-in IR LEDs, so treat it as **probably active** (inference) and confirm with the authors.
- **Stratification is feasible on real data:**
  - Active NIR: Real-NAID, Kim & Baek, probably TRNT.
  - Passive NIR (probably): IDD-AW.
  - Light-source sub-strata within IDD-AW: student-labelled.

### Gaps
- Not verified: actual IDD-AW archive contents (`*_polygons.json`, instance ids), whether the TRNT and DVD drive links are live, and TRNT's NIR band, illumination and night counts. The mobility.iiit.ac.in Safe-Seg'24 dataset page returned 502.
- Kim & Baek's label release and the source of their detection ground truth.

## Q6. Is the thesis protocol framing (P0–P4) applied to NIR guidance anywhere?

### Takeaway
No. The protocol cells for NIR guidance are scattered:
- P0: Kim & Baek, frozen YOLO on fused images, active NIR.
- P3: ICPR'24, DVN or FusionNet preprocessing then fine-tuned segmenters.
- Input-level fusion trained jointly with the model: IDD-AW stacking and TRNT AUNet.

No paper puts them side by side, adds P2 (raw-trained, enhanced test) or P4 (mixed), or contrasts NIR-guided with RGB-only enhancers under identical budgets.

### Cited Findings
- Kim & Baek (P0-type, one detector; RGB 0.756 / DVN 0.762 / theirs 0.809). — [arXiv 2411.18025](https://arxiv.org/html/2411.18025v2)
- ICPR'24 (P3-type: DVN smoothing → fine-tuned Mask2Former, 67.58; FusionNet → SegFormer, 68.32; RGB-only Mask2Former Swin-L, 68.54, 2nd place). — [arXiv 2409.05327](https://arxiv.org/abs/2409.05327)
- IDD-AW stacking, trained from scratch on stacked input; low light 62 → 63. — [arXiv 2311.14459](https://arxiv.org/abs/2311.14459)
- **For RGB-only enhancers, P0–P4 pieces exist** (LIME-Eval with 3 protocols; Sun 2022 P0; LoViF P0). The thesis's Paper 1 claims the full P0–P4 grid as its own novelty for RGB-only low-light detection. — `/home/user/lowlight/research/PUBLICATION_ROADMAP.md` §1.1; `literature/06_paper1_study_novelty.md`

### Inferences
- **The ICPR'24 numbers are already an embarrassment for the "NIR helps" story.** The RGB-only Mask2Former entry (68.54) beat DVN-preprocessed Mask2Former (67.58) and roughly tied the FusionNet entry (68.32). A hostile reviewer will cite this as evidence that NIR-guided preprocessing does not beat a strong RGB backbone. Paper B must reproduce this with matched backbones and CIs; it is effectively the P3 cell.
- **The P0–P4 framing is safe for NIR only if it cites Paper 1** (or the thesis) as the source of the protocol. It must not re-claim the protocol as novel. Otherwise it is self-overlap (see Q8).

### Gaps
- Per-condition ICPR'24 results (low light only) were not extracted. The report appears to give aggregate mIoU / SmIoU only.

## Q7. Safe to claim, must NOT claim, and must-cite

### Takeaway
Safe claims are those about **protocol × modality × photon-budget interaction** and about **architecturally reachable non-inferiority measured on held-out models**. Unsafe claims are anything about:
- "first downstream evaluation of NIR-guided enhancement";
- "first NIR fusion for detection";
- "first reliability gating";
- "first task-driven fusion";
- "first to show enhancement hurts";
- any formal "guarantee".

### Cited Findings

**Safe to claim (with the stated qualifiers).**
1. **First controlled comparison** of RGB-only enhancers, NIR-guided enhancers (DVN, SFM, FCENet, FD-INR-style INR) and naive stacking under the **same P0/P2/P3/P4 protocols** (P1 as reference), on real adverse-condition data for both segmentation and detection, with paired-bootstrap CIs.
   - Qualifier: cite ICPR'24 (P3-type DVN preprocessing on IDD-AW) and Kim & Baek (P0-type DVN baseline) as partial precedents.
   - Sources: [2409.05327](https://arxiv.org/abs/2409.05327), [2411.18025](https://arxiv.org/html/2411.18025v2)
2. **First stratification of NIR-guidance utility by NIR photon budget**: active vs passive illumination, and ambient source type (HPS/MH vs halogen vs white LED), grounded in measured lamp spectra. — [Elvidge 2010](https://pmc.ncbi.nlm.nih.gov/articles/PMC3274255/)
3. **First use of light-source spectral cues (NIR/RGB ratio statistics) to gate NIR guidance** in restoration or perception. Fredembach 2009 and Han 2024 use the cue only for colour constancy. — [EPFL](https://infoscience.epfl.ch/record/133341)
4. **First NIR-guided enhancer trained through frozen downstream task models** with an **input-relative** non-inferiority term. Qualifiers:
   - say "soft" or "constrained", not "guaranteed";
   - make the identity reachable;
   - acknowledge Kim & Baek trained NIR fusion through a frozen *stereo* network.
5. **First evaluation of NIR-guided restorers on passive-NIR night data.** This holds only if IDD-AW passivity is confirmed with the authors; otherwise say "NIR captured without a dedicated illuminator, per the dataset description".
6. **Detection benchmark derived from IDD-AW instance polygons**, released as boxes plus scripts. Say "first in a Q1/A* venue" unless ICNTE 2026's mAP50 is checked.

**Must NOT claim.**
- Enhancement or restoration hurts or is inconsistent for detection. (Sun NeurIPS 2022; [2409.14461](https://arxiv.org/abs/2409.14461); LIME-Eval; LoViF; the student's own Paper 1)
- The DPI / "enhancers add no information" argument as new. ([Turgeman & Tirer ICLR 2026](https://arxiv.org/abs/2512.21315), who also prove processing *can* help at finite sample size)
- NIR improves segmentation over RGB on IDD-AW. ([IDD-AW](https://arxiv.org/abs/2311.14459))
- First NIR-guided preprocessing evaluated on IDD-AW segmentation. ([ICPR'24](https://arxiv.org/abs/2409.05327))
- First frozen-detector / zero-shot evaluation of RGB-NIR fusion, or first NIR fusion "without fine-tuning". ([Kim & Baek CVPR'25](https://arxiv.org/html/2411.18025v2))
- First RGB-NIR night *detection* dataset, or "no NIR detection benchmark exists". (TRNT [AAAI'26](https://ojs.aaai.org/index.php/AAAI/article/view/37232); STF gated NIR [CVPR'20](https://arxiv.org/abs/1902.08913); PRAI 2022 vehicle NIR fusion)
- First reliability-, uncertainty- or availability-gated NIR fusion. (AUNet router; STF entropy; SFM modulation; DVN DIP; Kim & Baek learned weights)
- First task-driven or task-loss-trained fusion or enhancement. (TarDAL, SeAFusion, [TDFusion](https://arxiv.org/abs/2412.03240), CVPR'26 Customized Fusion, AMFusion [2403.01083](https://arxiv.org/abs/2403.01083), Sun 2022, DBDiff)
- First to use NIR for illuminant estimation, or "illumination-aware fusion" in the brightness sense. (Fredembach 2009; Han IEEE Access 2024; TCSVT 2026 2606.14248; IAF R-CNN / PIAFusion (mem))
- A "no-regret guarantee", "never worse than input", or "provably safe". It is only a training penalty, and with g=0 the output is E_rgb(x), not x.
- That white LEDs emit zero NIR. Elvidge: "virtually no emission beyond 800 nm"; IDD-AW NIR starts at 740 nm.
- That passive or night NIR "has never been studied". IDD-AW and its competition used it; claim only "not as a guidance / restoration setting with photon-budget analysis".
- That NIR helps in general, without leakage-controlled splits. ([2609.13265](https://arxiv.org/abs/2609.13265) shows NIR's apparent advantage vanishes once site leakage is controlled. Use IDD-AW's drive-sequence-disjoint split and state it.)
- Anything about P0–P4 protocol novelty itself (owned by Paper 1).

**Must-cite list (title, venue/year, id).**

*NIR-guided restoration*
- Yan et al., "Cross-Field Joint Image Restoration via Scale Map", ICCV 2013.
- Xiong et al., "Seeing in Extra Darkness Using a Deep-Red Flash", CVPR 2021.
- Jin et al., DarkVisionNet (DVN) + DVD, AAAI 2022, arXiv 2303.06834.
- Xu et al., "NIR-Assisted Image Denoising: A Selective Fusion Approach and A Real-World Benchmark Dataset", TMM 2025, arXiv 2404.08514.
- Wang et al., FCENet, "Complementary Advantages: Exploiting Cross-Field Frequency Correlation for NIR-Assisted Image Denoising", CVPR 2025, arXiv 2412.16645.
- FD-INR, arXiv 2604.16800.
- Niu et al., "NIR-assisted Video Enhancement via Unpaired 24-hour Data" (NVEU/FMSVD), ICCV 2023.
- Niu et al., "Toward Robust and 3D-Aware RGB-NIR Imaging in the Dark", ACM MM 2026, arXiv 2607.29684.
- Lv et al., "An Integrated Enhancement Solution for 24-hour Colorful Imaging", AAAI 2020, arXiv 2005.04580.
- Zheng, Jung, Yu, "Low Light Image Enhancement by Multispectral Fusion of RGB and NIR Images", ICIP 2020 (DOI 10.1109/ICIP40778.2020.9191211).
- Liu, Wang, Jung, "LRINet: Long-range imaging using multispectral fusion of RGB and NIR images", Information Fusion 2023 (DOI 10.1016/j.inffus.2022.11.020).
- SGDFormer / SANet cross-spectral stereo guided denoising (CVPR 2023 / Information Fusion 2025, arXiv 2404.00349).

*RGB-NIR perception and data*
- Shaik et al., IDD-AW, WACV 2024, arXiv 2311.14459.
- ICPR 2024 Safe-Seg competition report, arXiv 2409.05327.
- Kim & Baek, CVPR 2025, arXiv 2411.18025.
- Bie et al., TRNT/AUNet, AAAI 2026, arXiv 2602.06363.
- Bijelic et al., "Seeing Through Fog Without Seeing Fog", CVPR 2020, arXiv 1902.08913.
- CLEAR-IR, IROS 2026, arXiv 2510.04883.
- SpectraDINO, arXiv 2605.02258.
- MCubeS, CVPR 2022 (mem).
- Deng et al., PRAI 2022 (NIR fusion + YOLOv4 night).
- "Forward-Facing NIR Adds Little …", arXiv 2609.13265.

*Enhancement vs machine perception*
- Sun et al., "Rethinking Image Restoration for Object Detection", NeurIPS 2022.
- Wu et al., arXiv 2409.14461.
- LIME-Eval, arXiv 2410.08810.
- Cheng et al. (DBDiff), CVPRW 2026 LoViF.
- TaskGuard, arXiv 2609.08011.
- Turgeman & Tirer, ICLR 2026, arXiv 2512.21315.
- Morawski et al., TMM 2025, arXiv 2412.07693.
- **The student's Paper 1 / thesis** (protocols P0–P4, regret rate).

*Task-driven fusion*
- TarDAL, CVPR 2022, arXiv 2203.16220 (mem).
- SeAFusion, Information Fusion 2022 (mem).
- CDDFuse, CVPR 2023, arXiv 2211.14461 (mem).
- TDFusion, CVPR 2025, arXiv 2412.03240.
- AMFusion, arXiv 2403.01083.
- Yang et al., "Customized Fusion", CVPR 2026.
- UniRestore, CVPR 2025, arXiv 2501.13134.

*Illuminant physics*
- Elvidge et al., *Sensors* 2010 (PMC3274255).
- Fredembach & Süsstrunk, SPIE EI 2009 (DOI 10.1117/12.806431).
- Han et al., IEEE Access 2024 (DOI 10.1109/ACCESS.2024.3434574).
- TCSVT 2026, arXiv 2606.14248.
- "Visibility Constrained Wide-band Illumination Spectrum Design for Seeing-in-the-Dark", CVPR 2023, arXiv 2303.11642.

*Colorization (to delimit scope)*
- HAQAGen (Attri et al.), WACV 2026, arXiv 2601.01103 (WACV confirmed in the [CVF WACV 2026 list](https://openaccess.thecvf.com/WACV2026?day=all)).
- Gao et al., "Object-Aware NIR-to-Visible Translation", ECCV 2024.

### Inferences
- The must-cite list is long and largely thermal-fusion-heavy for Information Fusion and TCSVT. Reviewers there know TarDAL, SeAFusion and TDFusion well, so position Paper B against them in the first paragraph of related work.

### Gaps
- Items marked (mem) were not re-verified in this session: TarDAL, SeAFusion, CDDFuse, PIAFusion, IAF R-CNN, MCubeS.

## Q8. Scope boundary: not NIR→RGB colorization; overlap with the thesis roadmap

### Takeaway
**Colorization.** Paper B is not colorization if it is specified carefully. Its primary signal is the dark RGB frame; NIR is a guide; the output is judged by machine tasks; and chroma comes from RGB. The boundary blurs in pitch-dark regions, where R(x, n) would have to invent colour (CLEAR-IR, Niu MM'26 and NIR2RGB do exactly that).

**Thesis overlap.** Paper B overlaps the roadmap on two points: it reuses Paper 1's P0–P4 protocols and regret-rate metric, and it revives the "no-regret enhancement" variant that the roadmap retired at 3/10. It is distinct only through NIR (a second measurement), new datasets, segmentation, and the photon-budget axis. It must cite Paper 1 and not re-claim its contributions.

### Cited Findings
- **The roadmap's portfolio** is Paper 1 (RGB-only protocol × darkness study on ExDark, DarkFace and NOD; P0–P4; regret rate), Paper 2 (cross-fitted utility distillation) and Paper 3 (recognition vs localization). It says "The earlier DPI-Det design and the no-regret enhancement variant are retired, and their useful parts live inside Papers 1 and 2". It also lists the "regret rate" among Paper 1's required design. — `/home/user/lowlight/research/PUBLICATION_ROADMAP.md` §1, §1.1
- **The roadmap never mentions NIR**, RGB-NIR, IDD-AW, TRNT or Real-NAID; its datasets are ExDark, DarkFace, NOD and BDD-night. — same file (full read)
- **NIR→RGB translation and colorization neighbours:**
  - CLEAR-IR ("RGB styled images from emitter populated IR inputs").
  - Niu MM'26 (no clean RGB needed; Gumbel colour codes for the one-to-many NIR→RGB mapping).
  - Wide-band illumination for NIR2RGB, CVPR 2023.
  - HAQAGen, WACV 2026 (the group's colorization work).
  - Sources: [2510.04883](https://arxiv.org/abs/2510.04883), [2607.29684](https://arxiv.org/html/2607.29684), [2303.11642](https://arxiv.org/abs/2303.11642), [CVF WACV 2026](https://openaccess.thecvf.com/WACV2026?day=all)

### Inferences
- **To keep clear of colorization:**
  - (i) Restrict R(x, n) to luminance or high-frequency structure, for example a residual in Y or in a wavelet high band, and take chroma from E_rgb(x).
  - (ii) Report a "chroma-from-NIR" ablation showing no colour is synthesised.
  - (iii) State that no NIR-only input is ever mapped to RGB.
  - (iv) Exclude scenes with RGB SNR ≈ 0 from fidelity metrics, or report them separately.
- **To keep clear of the thesis:**
  - (i) State in the introduction that P0–P4 and the regret-rate metric come from [Paper 1].
  - (ii) Make the research question explicitly "does an *extra measurement* change Paper 1's conclusion?"
  - (iii) Use no ExDark, DarkFace or NOD headline results; they belong to Paper 1.
  - (iv) **Sequencing:** submit Paper 1 first (the roadmap targets March 2027), and have Paper B cite it as a preprint.
  - (v) Present the gated residual as a *NIR-specific* design: the illuminant-aware gate is the novelty, and non-inferiority is a constraint borrowed from Paper 1's metric. It should not be framed as reviving the retired RGB-only no-regret enhancer.
  - If Paper B were submitted before Paper 1, a reviewer seeing both could flag salami-slicing.

### Gaps
- `literature/05`–`07` were only grepped, not fully read. Whether Paper 2's per-object utility gate overlaps with Paper B's gate was not checked in depth. They look different: per-object distillation vs per-pixel NIR injection.

## Q9. Novelty scores, biggest reviewer objection and rebuttal experiment

### Takeaway
**Q1 journal: 6/10**, provided Paper B delivers four things:
1. light-source and illumination stratification with student-made labels;
2. a reachable identity (two gates);
3. held-out-model regret rates;
4. the RGB-only, matched-capacity, task-trained control.

Without the stratification it is **5/10**, reading as "TarDAL/TDFusion for NIR plus a benchmark". **A\*: 3–4/10**, since every component has a precedent and the passive-NIR gains may be small (IDD-AW low light: +1 mIoU from stacking).

**Biggest objection:** "The gains come from extra capacity and task-loss training, not from NIR; and your 'no-regret' fallback returns the RGB-only enhancer, which you yourselves show is harmful."

### Cited Findings
- **Room for gains is small on passive data and large on active data.**
  - IDD-AW low light: RGB 62, stack 63.
  - ICPR'24: an RGB-only Mask2Former entry (68.54) beat DVN-preprocessed Mask2Former (67.58) and roughly tied the FusionNet-fusion entry (68.32).
  - Active-NIR TRNT: RGB 77.0 → RGB+NIR 91.6 AP50.
  - Kim & Baek (active): 0.756 → 0.809 mAP.
  - Sources: [2311.14459](https://arxiv.org/abs/2311.14459), [2409.05327](https://arxiv.org/abs/2409.05327), [2602.06363](https://arxiv.org/html/2602.06363), [2411.18025](https://arxiv.org/html/2411.18025v2)
- **Learned gating is near its ceiling for fidelity**: SFM gains only about 0.23 dB over plain summation (wave-1 audit, [2404.08514](https://arxiv.org/html/2404.08514v4)).
- **The leakage critique**: NIR's advantage vanished under site-disjoint evaluation. — [2609.13265](https://arxiv.org/abs/2609.13265)

### Inferences
**Rebuttal experiment.** All arms run on IDD-AW (segmentation plus derived boxes) and TRNT-night, 3 seeds, paired-bootstrap CIs:
1. **Matched-capacity RGB-only twin.** The same architecture and task-loss training with n replaced by:
   - (a) zeros;
   - (b) the luminance of x (a "fake NIR", so capacity is matched);
   - (c) NIR from a *different* frame of the same sequence (keeps NIR statistics, removes information);
   - (d) real NIR.
   - The NIR-information effect is (d) − (b) and (d) − (c), reported per stratum.
2. **Identity reachability and held-out regret.** Use the two-gate design y = x + a ⊙ (E_rgb(x) − x) + g ⊙ R(x, n) and train through models A. Report:
   - regret rate (the fraction of images, or of objects, with L_task(y) > L_task(x)) on held-out frozen models B (a different family: CNN vs transformer segmenter; YOLO vs DETR), and on a held-out dataset (TRNT for an IDD-AW-trained enhancer);
   - against the per-image oracle max(x, E_rgb(x), y).
3. **Photon-budget dose-response.** Bin the IDD-AW low-light images by an *independent* NIR/RGB-ratio label: student-annotated light source (HPS/MH, LED, halogen headlights, mixed), checked against Elvidge's spectra.
   - Show that ΔmIoU / ΔAP from NIR, and the learned gate g, both increase monotonically with NIR photon budget.
   - Show the same on active sets (Real-NAID fidelity, TRNT).
   - A flat dose-response would falsify the story; pre-register this.
4. **Ablate the illuminant estimator vs a plain learned gate** (same parameters, without ratio or noise statistics as inputs), to show the physics-derived input matters. Without this, reviewers will say an SFM-style learned gate already does it.

**Expected-result targets.**
- **IDD-AW, low-light condition:** at least +2 mIoU over the RGB-only twin (arm b) and at least +1 mIoU over stacking (63), with regret rate ≤ 5% on held-out segmenters.
- **TRNT-night:** at least +3 AP50 over the RGB-only twin with a frozen detector.
- These are judgement-based thresholds, not literature numbers.

### Gaps
- No pilot numbers exist, so feasibility of the +2 mIoU target on passive IDD-AW is unknown and is the main risk.
- The scores are reviewer-judgement estimates, not derived from any metric.
