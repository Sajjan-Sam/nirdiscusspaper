# Paper 2 (Q1 journal): A falsifiable spectral-physics account of when NIR helps RGB–NIR segmentation

Target: IEEE TIP or Information Fusion (~Aug 2027). Shared context, calendar, gates and venues: [../common/FULL_REPORT.md](../common/FULL_REPORT.md).

## Paper A′ earns its Q1 even if the physics fails

### The science claim is open, even though every method component has a neighbour

The reframe audit's verdict is that **unseen-condition fusion is not new**. Seeing Through Fog trained on clear weather only and tested on unseen adverse weather, using a hand-crafted entropy cue ([arXiv 1902.08913](https://arxiv.org/abs/1902.08913)), and DCDA (ECCV 2026) defined type-open and severity-open weather protocols for LiDAR–radar fusion ([arXiv 2607.01983](https://arxiv.org/abs/2607.01983)). What is still open is a quantitative, per-pixel *spectral-physics* model of a trained RGB–NIR segmenter's marginal NIR utility, checked against cross-fitted counterfactual utility, together with a head-to-head of physics-constrained vs learned vs condition-token gates on held-out conditions in RGB-X segmentation. A new close threat is RAF (ECCV 2026), which explicitly supervises per-pixel reliability maps using cross-modal-similarity pseudo-labels ([arXiv 2607.04587](https://arxiv.org/abs/2607.04587)).

Novelty therefore rises from 5 to **about 6/10 for A\*** and from 7 to **7.5/10 for a Q1 journal**. A′ must lead with the scientific claim and present gates as the instrument that tests it ([reframe audit](notes/paperA_reframe_physics_ood.md)). The recommended title is **"When Does Near-Infrared Help? A Falsifiable Spectral-Physics Account of Modality Utility for Robust RGB–NIR Segmentation."** Avoid "Learning Where to Trust…", which collides with arXiv 2610.36672.

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
4. The first controlled benchmark of modern RGB-X architectures on IDD-AW. The Paper A audit rated this novel as infrastructure ([Paper A audit](notes/paperA_novelty_audit.md)).

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

**Data.** A′ uses the same IDD-AW pipeline and split as ALT-1′, with the full 26 level-3 classes. Report mIoU, SmIoU with the script's 8 traffic-participant classes, SmIoU with the paper's default important set (stating its members), and the mean safe-IoU over the 8 traffic-participant classes, all per condition. Train at 1024×768 with random scale 0.5–2.0, 768×768 crops and horizontal flips. Infer at 1024×768, upsample logits to native resolution, and state this explicitly, since prior work did not ([Paper A blueprint](notes/paperA_experiment_blueprint.md)).

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

**Running F4.** Hyperspectral City V2 has 128 bands over 450–950 nm, 19 Cityscapes classes and lossy H.264 compression ([dataset audit](../common/background_notes/wave1_datasets_audit.md)). The steps:

1. Integrate its per-pixel spectra through approximate JAI bands (B/G/R centres about 465/540/615 nm; NIR about 850 nm, truncated at 950 nm). The real JAI quantum-efficiency curves are unpublished.
2. Estimate per-class unique-NIR information. Validate the estimator on synthetic ground truth first; a reviewer in an ICLR 2026 thread demanded exactly this for MI claims ([venues note](../common/background_notes/venues_figures_and_paperC.md)).
3. Map Hyperspectral City V2's classes onto IDD-AW's.
4. Pass condition: ρ ≥ 0.5 with p < 0.05. With n ≈ 19–26 classes this is under-powered, so pre-register that a null result goes into a section, not the headline.
5. Describe this as integrating measured spectra through band curves, never as NIR→RGB conversion.

**Light-source labels for F2.** Reuse Paper B's protocol, about 3–4 person-days ([Paper B blueprint](../common/dropped_candidates/paperB_experiment_blueprint.md)). Two annotators label the dominant and secondary source on all ~1,000 low-light images (Label Studio, with RGB, NIR and a ratio map side by side, 10–15 s per image), and region boxes are drawn on 200 images. The target agreement is Cohen's κ ≥ 0.6; below that, merge classes into "NIR-rich" and "NIR-poor white". As a physical validation, a ratio-only classifier should reach AUC ≥ 0.75 against the human labels. India had about 13.1 million LED streetlights by November 2024 ([Parliament reply](https://eparlib.nic.in/bitstream/123456789/779754/1/AU3430.pdf)). IDD-AW's night frames are probably LED-dominated, so NIR-rich strata may be small.

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

Do not retrain Mask2Former Swin-L or InternImage-B. Each took about 4 A100-days in the competition; cite them instead ([Paper A blueprint](notes/paperA_experiment_blueprint.md)). The blueprint's controlled targets, StitchFusion-B2 ≥ B0 + 1.5 mIoU and per-condition gains largest in low light and rain, serve as sanity checks rather than claims.

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

