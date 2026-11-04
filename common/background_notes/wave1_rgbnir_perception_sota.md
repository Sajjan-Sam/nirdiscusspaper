## RGB–NIR prior-art report (searched up to 2026-11-03)

**Short version:** On MCubeS, the best all-modality (RGB+AoLP+DoLP+NIR) result I found is **FuseAdapt (NeurIPS 2026) at 57.62 mIoU**. The best RGB+NIR-only result is **StitchFusion at 54.59** (Swin-L backbone) or 53.21 (MiT-B4). IDD-AW has almost no RGB-X fusion literature after 2024.

Of the six candidates, none is killed outright. Two are mostly taken (C4, C6). The best opening is C1 with C2 used to check it.

### (1) SOTA landscape

**MCubeS (mIoU %; R = RGB, A = AoLP, D = DoLP, N = NIR)**

| Method | Venue / arXiv | RGB+N | RADN (all four) |
|---|---|---|---|
| MCubeSNet | CVPR'22 | – | 42.86 |
| CMNeXt | CVPR'23, 2303.01480 | – | 51.54 |
| U3M | Pattern Recognition'25, 2405.15365 | – | 51.69 |
| Any2Seg | ECCV'24, 2407.11351 | – | 51.91 |
| MLE-SAM | arXiv 2412.04220 | – | 51.02 |
| Cao et al. (BPCF/ScoreNet) | arXiv 2610.17800 (Oct'26) | – | 52.22 |
| MemorySAM (SAM2) | arXiv 2503.06700 | – | 52.88 |
| MAGIC | ECCV'24, 2407.11344 | – | 53.01 (number taken from FuseAdapt's table) |
| MMSFormer | IEEE OJSP, 2309.04001 | – | 53.11 |
| StitchFusion (MiT-B4 / +FFMs / Swin-L-22k) | ACM MM'25 per its own table, 2408.01343 | 51.67 / **53.21** / **54.59** | 51.74 / 53.92 / 55.96 |
| **FuseAdapt** (frozen ViT-B foundation model + per-modality LoRA, Predictive Modality Modeling) | **NeurIPS'26**, 2610.21779 | not reported | **57.62** (mean over RA/RAD/RADN: 56.30) |

- **No MCubeS numbers found for:** CMX, GeminiFusion (both shown as "–" in StitchFusion's table), DFormer/DFormerv2, Sigma, OmniSegmentor (NeurIPS'25, 2509.15096), DPLNet, UniRGB-IR, MM-SAM.
- **Inconsistency to note:** StitchFusion's own Table 3 and Table 7 disagree for the same setting (51.74 vs 53.92). Treat 53.92 as the "+FFMs" variant.
- **Different setting:** the ICCV'25 unimodal-bias paper (Zheng et al., 2505.06635) reports 41.64, but that is a modality-agnostic/missing-modality setting, not comparable.

**IDD-AW (WACV'24, 2311.14459; ICPR'24 competition report, 2409.05327). Format: mIoU / Safe mIoU**

| Entry | Input | mIoU / Safe mIoU |
|---|---|---|
| WACV'24 paper baseline | RGB | 64.70 / 60.56 |
| WACV'24 paper baseline | RGB+NIR | 67.30 / 62.26 |
| ICPR'24 #1, KweenCoders (FusionNet image fusion + SegFormer) | RGB+NIR | 68.32 / **64.73** |
| ICPR'24 #2, SixthSense (Mask2Former) | RGB only | **68.54** / 63.52 |

- I found **no** RGB-X fusion architecture (CMX, CMNeXt, StitchFusion, FuseAdapt, etc.) evaluated on IDD-AW. That is an open benchmark gap and a low-competition place to claim SOTA.
- **Heads-up:** a GitHub capstone repo (krnRaj03/iddaw-safe-segmentation, Fall 2026) plans "RGB–NIR fusion + hierarchy-aware loss" on IDD-AW. It has no results yet, but it overlaps with C5.

**Reliability-aware fusion, robustness and modality imbalance**
- Condition- or reliability-gated fusion:
  - CAFuser (RA-L'25, 2410.10791): RGB-derived condition token; 78.2 mIoU on MUSES.
  - RSGMamba (2604.12319): reliability-aware self-gating, RGB-D/RGB-T.
  - CrossWeaver (2604.02948).
  - UTFNet: evidential RGB-T fusion.
  - InfraNet QualGate (2607.03795).
  - Spectral Reliability Descriptors (2606.01173).
  - "Seeing Through Fog" (CVPR'20, 1902.08913): entropy-steered fusion.
- Imbalance / unimodal bias:
  - Wei et al., sample-level Shapley modality valuation (CVPR'24, 2309.06255).
  - QMF (ICML'23, 2306.02050) and Predictive Dynamic Fusion (ICML'24).
  - ICCV'25 functional-entropy regularizer; Keep the Balance (CVPR'25); EQUISeg (2509.24505); PDMP (2604.05773).
- Missing or noisy modality:
  - Sensor-failure benchmark (CVPRW'25, 2503.18445).
  - AnySeg (2411.17141), RMMSS (2505.12861), FuseAdapt.

**Auxiliary modality as a bridge / test-time adaptation**
- HeatNet (IROS'20, 2003.04645); MS-UDA (RA-L'21); CMDA with events (ICCV'23, 2307.15942).
- Night-TTA for RGB-T (IEEE TAI'23, 2307.04470); masked mutual learning RGB↔thermal (ICRA'25, 2505.06951).
- MM-TTA (CVPR'22, 2204.12667); CrossMatch (ICCV'23); MICDrop with depth (ECCV'24, 2408.16478).
- NIR-specific: Pandey et al. (ECCV'20, 2006.08696) adapts from the red channel to NIR.
- RGB-only Cityscapes→ACDC context: Refign-HRDA 72.1 (from an aggregator site, unverified against the paper), MIC 70.4, CoDA 72.6 on ACDC-All (ECCV'24). I could not verify CMA's number.

**Privileged modality (train with extra modality, deploy RGB-only)**
- Hoffman et al., modality hallucination (CVPR'16); Garcia et al. (1810.08437).
- HeatNet distils RGB-T into RGB-only for night.
- CEKD (RA-L'23); HalluciDet (WACV'24, 2310.04662).
- IR-privileged UAV detection deployed on RGB only (AAAI'26, doi 10.1609/aaai.v40i9.37692).

### (2) Verdict per candidate

| # | Closest prior art | What is still open | Novelty (1–10) | Main reviewer risk |
|---|---|---|---|---|
| **C1** physics-gated fusion | Schaul et al. ICIP'09; Feng et al. ICIP'13 (RGB–NIR dissimilarity → airlight and haze map); Dümbgen EI'18; Laha 2203.14085; HSVF (IEEE TMM, 2507.03893; single β, introduces the VNHD RGB-NIR haze dataset with 6-class labels); Bishoff 2305.11347 (hand-scaled NIR fog augmentation); PHMDet (depth-based); DarkVisionNet (AAAI'22) | Nobody uses a **multi-band Ångström-coupled** model, β(λ)∝λ^-γ shared across R, G, B and NIR, to produce per-pixel **reliability for segmentation fusion**. A per-band extinction model fitted to measured data is also missing. | **6** (7 if reframed as below) | Fog, rain and snow droplets are large, so scattering is nearly wavelength-independent (γ≈0) and NIR gains little there (Bijelic IV'18, 1912.03238; HSVF itself says "weak wavelength dependence"). On IDD-AW the physics may only matter for haze and low light. MCubeS is clear-weather, so the prior does nothing there. The gate could be dismissed as "just extra input channels." |
| **C2** counterfactual per-pixel utility supervising the gate | Wei CVPR'24 (sample-level Shapley, used for resampling); QMF / Predictive Dynamic Fusion (sample-level confidence tied to loss); RSFNet (2306.10364; saliency pseudo-labels pick a modality); Modality-Aware SAM (2510.24919) and CGAL (2510.26289), both sample-level; IAF / illumination-aware weighting (1802.09972); 2604.10702 shows learned gates collapse to static priors | **Dense, cross-fitted** (held-out-fold) per-pixel Δloss used as the gate's target. I found no prior example. | **6** | Compute cost; "just an auxiliary loss"; gains may be about +1 mIoU; in-sample Δloss≈0 if not cross-fitted. |
| **C3** NIR as domain bridge / TTA | HeatNet, MS-UDA, CMDA, Night-TTA, ICRA'25, MM-TTA, CrossMatch, Pandey ECCV'20 (red≈NIR) | NIR specifically; clear→fog/rain/snow rather than day→night; source-free TTA using "NIR as a near-domain view" of an RGB model | **5** | "Thermal/event methods ported to NIR." IDD-AW is fully labelled, so the UDA protocol looks artificial. |
| **C4** NIR as privileged information | Hoffman'16, Garcia'18, HeatNet, CEKD, HalluciDet, AAAI'26 IR-privileged UAV detection | Only "NIR + IDD-AW" is new | **3** | Incremental. NIR is close to RGB, so there is little to hallucinate. |
| **C5** hierarchical conformal / selective segmentation | Adaptive Hierarchical Certification for segmentation (ICML'24, 2402.08400; Cityscapes and ACDC, backs off to coarser classes); Hierarchical Selective Classification (NeurIPS'24); hierarchical conformal classification (2501.19038); conformal segmentation (2405.05145, 2602.10045); Hierarchical Insights (2404.06124) | Coverage-guaranteed hierarchical prediction sets for dense prediction with modality-conditioned calibration tied to Safe mIoU | **4–5** | Reads as an uncertainty-quantification paper where NIR is tangential. Pixel exchangeability assumptions are shaky. The capstone repo overlaps. |
| **C6** synthetic NIR for pretraining | Pix2Next (2409.16706; RANUS, IDD-AW); Texture-Shape Bias Balancing (ECML-PKDD'26, 2606.15072; SDXL+LoRA turns GTA5 images NIR-like); RGB→NIR/thermal translation (J. Imaging'25); Gated2Depth NIR heuristic; FusionRS (2606.17020, 600K diffusion pseudo-IR pairs); M-SpecGene (ICCV'25, but RGB-thermal) | Pretraining a fusion model on synthesized NIR and evaluating on real NIR | **4** (6 if degradations are applied *after* NIR synthesis) | Fatal objection: generated NIR is a function of RGB, so it carries no complementary information (data-processing inequality). It only helps if NIR is synthesized from the clean image and then wavelength-dependent degradations are applied. |
| **C7a** illuminant-spectrum-aware night fusion | Low-light RGB-NIR fusion (DarkVisionNet; 2604.16800); CAFuser; illumination-aware weighting (Guan '18) | At night, NIR reliability depends on the light source's spectrum: halogen headlights (and probably high-pressure sodium lamps) emit strongly in NIR, while white LEDs emit almost nothing beyond about 750 nm. A per-region illuminant estimate could gate fusion. This physics is background knowledge I did not verify by search. | **6** | No illuminant labels; the low-light IDD-AW subset is small; hard to verify. |
| **C7b** first RGB-X benchmark on IDD-AW plus a spectral corruption suite | Sensor-failure benchmark CVPRW'25; Bishoff'23 | Running CMX, CMNeXt, StitchFusion, DFormer, Sigma and FuseAdapt on IDD-AW, plus a physically correct per-band corruption suite ("MCubeS-C" / "IDD-AW-C") | **3** on its own | Infrastructure, not an A*-level contribution alone. Still necessary for any IDD-AW SOTA claim. |

### (3) Top-2 A*-worthy directions

**#1: Spectrally-coupled image-formation reliability for RGB–NIR fusion (C1 reframed, with C2 as the verification).**
- **Model:** each of the four bands follows I_c = J_c·t_c + A_c(1−t_c) + n_c, with t_c = exp(−τ·(λ_c/λ₀)^−γ). The optical depth τ and Ångström exponent γ are shared across bands. Each band's noise n_c gets a Poisson-Gaussian model calibrated from the sensor, which covers low light.
- **Why it is learnable:** four observations versus τ, γ and A makes the system over-determined. A small estimator can therefore be trained self-supervised with a physics-consistency loss plus spectrally correct synthetic degradations.
- **The gate:** the model yields a per-band contrast-to-noise reliability r_c ∝ t_c·|∇J_c|/σ_c. This gates a standard RGB-X backbone, for example a StitchFusion- or CMNeXt-style encoder.
- **Turning the γ≈0 problem into a strength:** the estimator should recover γ≈0 in fog and rain and γ>1 in haze. That is a falsifiable prediction no learned gate makes.
- **Verification with C2:** show that r correlates with cross-fitted counterfactual NIR utility.
- **Evaluation:** report per-condition gains on IDD-AW (with C7b baselines), MCubeS RGB+N (a sanity check, aiming to match or beat 53.21 on MiT-B4), a spectral-corruption suite, and VNHD if it is released (availability unverified).
- **Why it's novel:** the closest classical work (Feng'13, Schaul'09) is about dehazing, not perception fusion. The deep work either uses a single β (HSVF) or hand-tuned NIR factors (Bishoff'23).
- **Feasible on one GPU.**

**#2: Cross-fitted counterfactual utility as dense supervision for fusion gates (C2, generalized to RGB-X).**
- **Method:** train K=2–3 fold models. On held-out pixels, compute ΔCE_i = CE(fused without NIR) − CE(fused). Use it as a regression/ranking target for per-pixel gates. Cross-fitting in the style of double machine learning avoids the in-sample collapse where Δloss≈0.
- **Theory hook:** under squared or log loss, the optimal gate is a monotone function of the conditional utility.
- **Motivation:** direct evidence that end-to-end gates degenerate into static priors (2604.10702) and the dense-prediction imbalance literature (ICCV'25, EQUISeg).
- **Differentiation:** Wei CVPR'24, QMF and Predictive Dynamic Fusion are sample-level and classification-centric. RSFNet uses saliency, not utility.
- **Evaluation:** IDD-AW, MCubeS RGB+N, and one RGB-T set (MFNet) to show generality.
- **Compute:** feasible with MiT-B2 on IDD-AW's roughly 3.4k training images.
- **Risks:** modest gains, and the need to show the gate is calibrated against true utility.
- **Combining with #1:** C1 and C2 combine naturally into one paper: a physics prior plus counterfactual verification.

**Backup:** C3 as source-free TTA, using the RGB model's prediction on NIR treated as a near-red-channel view, combined with physics-weighted consensus.

**Unverified items:**
- MAGIC's 53.01 (taken from FuseAdapt's table, not MAGIC's own paper).
- Refign-HRDA 72.1 (from an aggregator site).
- StitchFusion's 51.67 vs 53.21 RGB+N mirror discrepancy (I used the paper's own table).
- Whether VNHD is publicly available.
- Whether QMF evaluates segmentation.
- The NIR emission of sodium lamps.
- Venues for MLE-SAM, MemorySAM, CrossWeaver, RSGMamba and 2610.17800 (all arXiv only as far as I found).
