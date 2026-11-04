# Paper A′ reframe audit: does a spectral-physics account of when NIR helps, with physics-informed gates that generalize to unseen conditions, reach A* novelty?

Searched up to 2026-11-03. Sources: arXiv API (abstracts and metadata), arXiv/ar5iv HTML full-text reads, WebSearch, PMC and CVF pages. No code was run. This note builds on `paperA_novelty_audit.md` (C1 prior art: TIER-MoE 2607.27289, 2608.15261, GAUGE 2608.05608), `paperA_experiment_blueprint.md` (descriptors, IDD-AW-C, datasets) and `wave1_spectral_physics_imaging.md` (Mie vs haze physics, JAI camera). Their findings are not repeated here.

Tags: [UV] = unverified; [mem] = from memory, not re-fetched this session.

**Headline**
- The reframe moves Paper A from a pre-empted *method* claim to a *falsifiable scientific claim plus an OOD protocol*. That raises A* novelty modestly, from **5 to about 6/10**. It is not a step change.
- Two anchoring ideas already have strong precedents:
  - Hand-crafted reliability steering that generalizes to unseen weather: **Seeing Through Fog**, CVPR 2020, whose full title ends "…Deep Multimodal Sensor Fusion in Unseen Adverse Weather".
  - A held-out-weather ("open-weather") evaluation protocol for sensor fusion: **DCDA**, ECCV 2026, 2607.01983, for LiDAR–4D radar detection.
- Wavelength-aware NIR corruptions also exist in a heuristic form for overhead RGB-NIR: **Bishoff et al. 2023**.
- What is still open: a quantitative, per-pixel *spectral-physics* model of a trained RGB–NIR segmenter's marginal NIR utility, verified against cross-fitted counterfactual utility. Also open: a head-to-head test of physics-constrained vs learned vs condition-token gates on held-out conditions in RGB-X segmentation. I found neither anywhere.
- A new close threat for the "dense supervised gate" part: **RAF** (ECCV 2026, 2607.04587) explicitly supervises per-pixel reliability maps.

---

## Q1. Domain generalization and unseen-condition robustness of multimodal (RGB-X) fusion gates. Does any work test leave-one-condition-out or unseen-weather generalization of fusion gates? Do physics or hand-crafted descriptors generalize better than learned gates?

### Takeaway
**Unseen-condition fusion is not new; doing it for RGB-X segmentation fusion gates is.**
- **Already done:**
  - Training on clear weather only and testing on unseen adverse weather with a hand-crafted (entropy) reliability cue: Seeing Through Fog, CVPR 2020, detection.
  - A structured held-out weather-type protocol for fusion: DCDA, ECCV 2026, LiDAR–4D radar detection on K-Radar.
- **Not found:**
  - Any RGB-X *segmentation* paper with leave-one-condition-out (LOCO) or cross-dataset evaluation of its fusion gate. CAFuser only shows a qualitative figure for unseen scenes.
  - Any paper, in any modality pair, that directly compares physics-descriptor gates with learned gates on held-out conditions.
- So A′'s headline is open as stated for RGB–NIR segmentation. It must not be phrased as "first fusion that generalizes to unseen weather".

### Cited Findings
**Seeing Through Fog** (Bijelic et al., CVPR 2020; full title "Seeing Through Fog Without Seeing Fog: Deep Multimodal Sensor Fusion in Unseen Adverse Weather") — [arXiv 1902.08913](https://arxiv.org/abs/1902.08913); details from [ar5iv full text](https://ar5iv.labs.arxiv.org/html/1902.08913).
- **Training data:** "we only use the clear weather portion of the proposed dataset for training". The model "generalizes robustly to unseen weather conditions".
- **The cue is hand-crafted:** "The entropy is calculated for each 8 bit binarized stream", from local histograms over 16×16 px patches.
- **The steering is learned:** "The steering process is learned purely on clean weather data". Sensor streams are dropped with p = 0.5 during training.
- **Ablation (Table 2, AP hard), Deep Entropy Fusion vs Deep Fusion (no adaptive layer):**

  | Condition | Deep Entropy Fusion | Deep Fusion |
  |---|---|---|
  | Clear | 79.46 | 77.82 |
  | Light fog | 84.90 | 79.63 |
  | Dense fog | 76.69 | 73.93 |
  | Snow/rain | 77.85 | 70.51 |

- There is **no** ablation of entropy against a learned reliability cue.
- Uses gated NIR at 808 nm.

**DCDA**, "Open-Weather Robust 3D Detection via Dual-Critic Diffusion Alignment" (arXiv 2607.01983; comments: "ECCV 2026 camera-ready") — [arXiv](https://arxiv.org/abs/2607.01983); [HTML](https://arxiv.org/html/2607.01983).
- **Motivation:** "most methods are constrained by a closed-world assumption, implicitly requiring training and test weather to align in both type and severity".
- **Protocols on K-Radar:**
  - **Type-open:** train on Normal + Rain + Sleet; Overcast, Fog, LightSnow and HeavySnow are seen only at test time.
  - **Severity-open:** Robo3D-style synthetic fog/snow/wet ground; train Light, test Medium/Heavy.
  - **Type+severity-open:** combines both.
- **Baselines** include gated or adaptive-weight fusion (L4DR, InterFusion). They degrade "noticeably" on unseen weather.
- **Gains:** DCDA gains +3.83 BEV AP and +7.73 3D AP over L4DR on unseen types.
- It deliberately avoids weather modelling ("Rather than modeling specific weather types"). There is **no** physics-based comparator.

**3D-LRF** (Chae, Kim & Yoon, CVPR 2024, pp. 15162–15172) — [CVF PDF](https://openaccess.thecvf.com/content/CVPR2024/papers/Chae_Towards_Robust_3D_Object_Detection_with_LiDAR_and_4D_Radar_CVPR_2024_paper.pdf); summary via [WebSearch](https://mlanthology.org/cvpr/2024/chae2024cvpr-robust).
- WRGNet gates radar flow using "a weather-conditioned feature from a pre-trained lightweight weather classifier trained on images".
- This is a categorical condition gate, evaluated on K-Radar's seen weather categories.

**CAFuser** (RA-L 2025, [arXiv 2410.10791](https://arxiv.org/abs/2410.10791); [HTML](https://arxiv.org/html/2410.10791)).
- **No held-out-condition, cross-dataset or LOCO metric.** The only related item is a qualitative look at how fusion weights adjust "in scenes that were not encountered during training" (Fig. 5).
- **Gating comparison is in-distribution only** (MUSES, Table V, PQ): mean fusion 59.1, random weights 58.5, learned static weights 59.1, condition-token (CAA) 59.4.
- **MUSES** covers "day and night for rain, snow, fog, and clear weather" (8 conditions) with RGB, lidar, radar and event camera. It has **no NIR**.

**DGFusion** ([arXiv 2509.09828](https://arxiv.org/abs/2509.09828)): adds "spatially varying local depth tokens" plus a global condition token to adapt fusion "to the spatially varying reliability of each sensor across the scene, which largely depends on depth". SOTA on MUSES/DeLiVER, evaluated in-distribution (abstract).

**Other RGB-X segmentation work that touches conditions, none of it LOCO:**
- Multimodal SAM-adapter ([2509.10408](https://arxiv.org/abs/2509.10408)): RGB-easy/RGB-hard splits of DeLiVER and FMB.
- "Reducing Unimodal Bias in Multi-Modal Semantic Segmentation" ([2505.06635](https://arxiv.org/pdf/2505.06635)): MUSES and DELIVER.
- Search results: [WebSearch](https://arxiv.org/abs/2509.10408).

**Spectral Reliability Descriptors** ("Bridging Multimodal Fusion and Expert Routing via Spectral Reliability Descriptors for Robust Object Detection", [arXiv 2606.01173](https://arxiv.org/html/2606.01173), no venue). Details from the HTML read.
- **Descriptor:** a parameter-free 7-D vector (low/high-band energy, amplitude ratio and phase consistency, plus global cross-modal correlation). The consuming SRF gate is a *learned* linear projection.
- **Test-time corruptions:** six (blur, low-light, noise, modality drop, 10/20 px misalignment). Retention: 87.9% baseline, 92.0% SRF+Desc, 91.9% learned uncertainty gate, 95.0% RCER.
- **Cross-dataset retention:** M3FD 90.8 → 96.2, DroneVehicle 87.9 → 95.0, FLIR-Aligned 91.4 → 95.7.
- **No content-only learned-gate numbers** appear in the cross-dataset tables. The descriptor-vs-content gate comparison (87.8 vs 87.2 mAP50) is on clean M3FD only.
- So even SRD does not show "descriptor gates generalize better than learned gates" under held-out conditions.

**Multimodal domain generalization literature.**
- A survey ([arXiv 2501.18592](https://arxiv.org/abs/2501.18592), v4 Sep 2025) covers multimodal DA, test-time adaptation (TTA) and DG "with applications ranging from action recognition to semantic segmentation".
- The only 2025 RGB-X DG segmentation paper found is DSSS ([2505.07050](https://arxiv.org/abs/2505.07050)), RGB-D synthetic→real style DG. It is not weather-LOCO and not about gates.
- A WebSearch for RGB-T DG to unseen domains found none from 2025–2026 ([search results](https://arxiv.org/abs/2505.07050)).

**Gate behaviour under shift (from the earlier audit):**
- 2604.10702 (prostate MRI) shows learned gates may collapse to a static prior, depending on backbone ([arXiv](https://arxiv.org/abs/2604.10702)).
- RAF reports "gating without proper supervision harms detection" ([HTML](https://arxiv.org/html/2607.04587)).

**IDD-AW facts that matter for LOCO** ([HTML](https://arxiv.org/html/2311.14459)):
- 5,000 annotated images: Rain 1,500, Fog 1,500, Low light 1,000, Snow 1,000. 202 sequences in total, with no per-condition breakdown.
- Captured "at different locations in India comprising urban, rural, and even hilly areas". Fog is described only as "fog/foggy".
- Nothing is said about night illumination or active NIR.
- InternImage-B per-condition mIoU:

  | Input | Rain | Fog | Low light | Snow | All |
  |---|---|---|---|---|---|
  | RGB | 62 | 64 | 62 | 53 | 64 |
  | NIR | 61 | 58 | 57 | 51 | 61 |
  | RGB+NIR | 66 | 65 | 63 | 53 | 67 |

**Cross-dataset targets:**
- **MCubeS** (CVPR 2022; [CVF PDF](https://openaccess.thecvf.com/content/CVPR2022/papers/Liang_Multimodal_Material_Segmentation_CVPR_2022_paper.pdf); rig per [Dataset Ninja](https://datasetninja.com/multimodal-material-segmentation) and [project page](https://vision.ist.i.kyoto-u.ac.jp/research/mcubes)).
  - Rig: 2× LUCID TRI050S-QC RGB-polarization cameras, a **FLIR GS3-U3-41C6NIR-C** NIR camera and a Livox Mid-100.
  - 500 sets from 42 street scenes, split 302/96/102, 20 material classes.
  - Its NIR comes from a different camera than IDD-AW's JAI FS-3200D prism camera.
- **Real RGB-NIR haze sets:**
  - VNHD (HSVF, TMM; [2507.03893](https://arxiv.org/html/2507.03893v1)) has no release (blueprint).
  - The ARRI RGB/NIR data set (2017) is not a haze set ([ARRI](https://www.arri.com/en/company/rgb-nir-data-set)).
  - The IEEE DataPort "Infrared-Visional Dehaze Dataset" has 300 registered pairs, an unknown IR band and no labels per the search summary ([DataPort](https://ieee-dataport.org/documents/infrared-visional-dehaze-dataset)).

### Inferences
- **Precise open gap (safe with "to our knowledge"):**
  - No LOCO or cross-camera evaluation of *RGB-X segmentation* fusion gates.
  - No head-to-head of physics-constrained vs learned vs condition-token gates on held-out conditions.
  - No physics model that predicts *which* held-out condition a gate should or should not trust NIR in.
- **Must-cite antecedents:**
  - Seeing Through Fog: the original "hand-crafted cue + unseen weather" claim.
  - DCDA: the open-weather protocol in fusion.
  - 3D-LRF and CAFuser: categorical condition gates.
  - SRD: parameter-free descriptors with unseen corruptions.
- **Structural argument, the strongest intellectual hook:**
  - Categorical condition gates have no representation for a held-out condition. These are CAFuser's text-supervised condition token and 3D-LRF's weather classifier. Under LOCO the held-out condition is forced onto one of the seen classes.
  - Continuous physical descriptors (per-band SNR, implied Ångström exponent q̂, NIR/VIS illuminant ratio) can represent a new condition. They extrapolate **only if the gate's functional form extrapolates**, e.g. sign-constrained logistic, monotone or lattice form.
  - "Physics inputs fed into an unconstrained MLP" is *not* a physics gate. That arm must be in the table, or reviewers will say the gain is input engineering. This is an inference; I found no paper testing it.
- **Where IDD-AW LOCO can and cannot show a physics advantage:**
  - **Held-out low-light:** the best case. The SNR factor extrapolates monotonically; a learned gate trained on daytime rain/fog/snow has never seen photon-starved frames.
  - **Held-out fog:** physics predicts NIR utility ≈ 0, since Mie scattering is wavelength-flat (wave1). The test is whether learned gates *over-trust* NIR. This is measured by pixel negative-transfer rate (NTR), not mIoU.
  - **Held-out snow:** fused equals RGB (53/53), so physics predicts u ≈ 0.
  - **Held-out rain** has the largest fused gain (+4). Scattering by large drops is wavelength-flat, so physics attributes this gain to material contrast (overcast, vegetation) rather than scattering. This is a falsifiable decomposition, not yet tested.
- **Effect sizes will be small.** Per-condition fused gains are +4, +1, +1 and 0. LOCO mIoU differences between gate arms may sit inside seed noise. The headline must rest on dense metrics: gate–utility Spearman on the held-out condition, null-calibrated pixel NTR and sign-AUROC. mIoU is secondary.
- **Cross-dataset IDD-AW → MCubeS tests only the SNR and material factors.** MCubeS is daytime and clear, so there is no scattering or illuminant variation.
  - Label spaces differ, so transfer the *gate*, not the segmenter: re-train the MCubeS host with NIR dropout, plug in the IDD-AW-fit gate, and score ρ(g, u_MCubeS).
  - The physics gate can be re-parameterised with the FLIR camera's NIR band; a learned gate cannot. This is a concrete, publishable asymmetry.
  - Report MCubeS's `NIR_warped_mask` validity factor separately so it does not inflate the physics gate's score.
- **The haze leg has no labelled real RGB-NIR set**, since VNHD is unreleased. Haze evidence will therefore be synthetic (IDD-AW-C) unless the HSVF authors share VNHD. A* reviewers will discount a synthetic-only haze result.

### Gaps
- I did not read the DCDA, 3D-LRF or L4DR full method sections beyond the DCDA HTML summary. L4DR's gate design is from DCDA's description.
- No exhaustive search of 2026 CVPR/ECCV proceedings (non-arXiv) for RGB-X segmentation LOCO. arXiv-API queries for "unseen weather", "held-out weather" and "unseen conditions" combined with fusion/multimodal returned only DCDA among fusion papers ([arXiv API](https://export.arxiv.org/api/query?search_query=abs:%22unseen%20weather%22)).
- SAMFusion's venue (ECCV 2024) is [mem]. The arXiv record 2508.16408 lists none.
- Whether MCubeS's NIR camera used a long-pass filter, and its exact band, is [UV].

---

## Q2. Do wavelength-dependent corruption benchmarks for RGB-NIR or multispectral perception exist (Bishoff 2305.11347; SynFog 2403.17094; HSVF 2507.03893; others)?

### Takeaway
**A wavelength-aware NIR corruption already exists:** Bishoff et al. 2023, overhead RGB-NIR segmentation, applies fog/haze with reduced severity in NIR and dims snow by 40% in NIR. So "first wavelength-aware corruption for RGB-NIR" is **not** safe.

**Still not found:**
- A *physically parameterized* per-band suite (Ångström/Kim β(λ), per-band Poisson–Gaussian low light with illuminant variants) for *driving* RGB–NIR segmentation.
- Above all, **matched control pairs**: spectral vs wavelength-flat haze at equal visibility, and LED-like vs halogen-like low light at equal visible exposure.

Other suites are visible-only (SynFog, FogDrive), generic-noise (the sensor-failure benchmark) or image-fusion (AWMM-100K).

### Cited Findings
**Bishoff et al.**, "Quantifying the robustness of deep multispectral segmentation models against natural perturbations and data poisoning" — [arXiv 2305.11347](https://arxiv.org/abs/2305.11347); quotes via [search summary of the PDF](https://arxiv.org/pdf/2305.11347). SPIE Defense + Commercial Sensing 2023 per the search summary [UV]; [PNNL record](https://www.pnnl.gov/publications/quantifying-robustness-deep-multispectral-segmentation-models-against-natural).
- **Fog/haze:** "NIR light can more easily penetrate haze and smoke, so a realistic implementation of fog/haze should have a reduced application in the NIR band".
- **Snow:** NIR "reflectance drops to 0.6 in the NIR band; reducing the brightness of the added corruptions by 40%".
- **Finding:** models with NIR "generally performed better under natural perturbations, especially in the recognition of vegetation categories". The effect "differs based on fusion architecture and input data".
- Overhead imagery only.

**SynFog** ([arXiv 2403.17094](https://arxiv.org/abs/2403.17094)).
- End-to-end physically based imaging simulation with "sky light and active lighting conditions, as well as three levels of fog density".
- Visible only, with fog treated as wavelength-independent (wave1 note).

**FogDrive** ([arXiv 2607.22698](https://arxiv.org/abs/2607.22698), Jul 2026).
- CARLA, 660 scenes, about 133k frames, 50:50 day/night.
- "Physically consistent fog is modeled independently on camera channels (Koschmieder model) and LiDAR channels (Beer-Lambert law) at three calibrated visibility densities (160m, 100m, 50m)".
- Sensors: RGB, depth and semantic cameras, LiDAR and radar. **No NIR.**

**HSVF / VNHD** ([2507.03893](https://arxiv.org/html/2507.03893v1), IEEE TMM): a real VIS–NIR long-range haze set. The audit notes it uses a single β and states Mie's "weak wavelength dependence". Not a corruption benchmark, and not released.

**AWFusion / AWMM-100K** ([2402.02090](https://arxiv.org/abs/2402.02090)): 100k IR–visible pairs across "rain, haze, and snow conditions". IR is degraded with "only general contrast and detail reduction" (audit). This is image fusion, not per-band physics for segmentation.

**Sensor-failure benchmark** (CVPRW 2025 oral, [2503.18445](https://arxiv.org/abs/2503.18445)): Entire-Missing, Random-Missing and Noisy Modality scenarios, with Gaussian and salt-and-pepper noise. Generic, not wavelength-aware.

**Physics parameters for a spectral suite** (from the blueprint):
- **Kim model:** β = (3.91/V)(λ/550)^−q, with q = 0 for V < 0.5 km ([Radioengineering 2010](https://www.radioeng.cz/fulltexts/2010/10_02_223_227.pdf)).
- **Caveat:** contested at short visibility by Ijaz et al. 2013 ([Northumbria](https://nrl.northumbria.ac.uk/id/eprint/16216)).

**Real-world band comparison in fog** (Pinchon et al., AMAA 2018, [HAL](https://hal.archives-ouvertes.fr/hal-01406023); numbers from [ULIS slides](https://fr.slideshare.net/ULIS/all-weather-vision-for-automotive-safety-which-spectral-band)).
- "NIR provides best SNR" in adverse conditions.
- Road-marking detection range with headlights: 53 m visible RGB, 60 m extended NIR, 63 m SWIR.
- LWIR is best for pedestrians in fog.
- Conclusion: the NIR advantage in fog is modest, not "penetration".

### Inferences
- **Safe claim C4′:** "a physically parameterised, per-band RGB–NIR driving corruption suite with matched spectral/flat haze and LED/halogen low-light control pairs, used as a falsification instrument". Cite Bishoff as the heuristic overhead precedent and SynFog/FogDrive as visible-only physics simulators.
- **The control pairs are the novel scientific element.** At matched visibility V, the spectral-haze vs flat-haze contrast isolates the λ-dependence of NIR utility. This is the cleanest physics test available without hardware. No found benchmark has it.
- **Credibility requirement.** Rank-correlate model robustness on IDD-AW-C fog and low-light with real IDD-AW fog and low-light (the blueprint suggests this). Without it, reviewers will treat the suite as synthetic.
- **The "NIR penetrates haze" premise is already in Bishoff.** A′ must go further: show *how much* NIR utility changes with q at matched V, and that it is ≈ 0 at q = 0.

### Gaps
- No search of non-arXiv remote-sensing venues (TGRS, ISPRS) for spectral corruption benchmarks in multispectral classification. Some may exist (unsearched).
- Bishoff's exact fog-severity reduction factor for NIR was not read (search summary only).

---

## Q3. Is illuminant spectrum or light-source type used as a cue in multimodal perception or fusion? Are material spectral-reflectance priors used in RGB-NIR or multispectral segmentation fusion?

### Takeaway
**Illuminant type.**
- Lamp-type recognition from spectral bands is established in *night-time remote sensing* (Elvidge 2010; SDGSAT-1, UAV and ISS studies) and in *computational imaging*, via AC flicker (ACam, CVPR 2017).
- I found **no perception or fusion paper that uses illuminant spectrum or lamp type to gate NIR**. Illumination-aware RGB-T fusion uses illumination *intensity* (day/night), not spectrum.
- The physics is solid, but it is the hardest factor to test on IDD-AW. India has 13.1 M LED streetlights under its national programme (SLNP), the night frames carry no illuminant labels, and the illuminant effect may be absorbed by the measured per-band SNR.

**Material priors.**
- Spectral-index input channels are old: NDVI/EVI in Freiburg Forest/AdapNet/CMoDE (2016–17), and NDVI/NDWI in MeCSAFNet (2026).
- The Galway group does band selection on Hyperspectral City V2 (H-City), with no fusion gating.
- SpectraDINO is modality conditioning, with no reflectance prior.
- A library- and camera-curve-derived VIS-vs-NIR contrast prior that *predicts NIR utility* was **not found**.

### Cited Findings
**Lamp spectra** (Elvidge, Keith, Tuttle & Baugh, "Spectral Identification of Lighting Type and Character", *Sensors* 10(4):3961–3988, 2010; 43 lamps, 9 types, 350–2,500 nm) — [PMC3274255](https://pmc.ncbi.nlm.nih.gov/articles/PMC3274255/); [MDPI](https://www.mdpi.com/1424-8220/10/4/3961).
- **High-pressure sodium:** "The strongest emission line is from the set of sodium emissions at 819 nm". Further IR lines at 767, 1,269, … nm.
- **Metal halide:** strong 819 nm emission, plus "915, 937, 1,013 …" nm.
- **Incandescent and quartz halogen:** blackbody-like, with peak radiance at 900–1,050 nm.
- **Low-pressure sodium:** an IR-reflecting coating "reduces the emission of infrared light".
- **Fluorescent:** "virtual no emission in the infrared".
- **White LED:** "The measured LED lamps had virtually no emission beyond 800 nm".
- **Feasibility of lamp-type ID:** "feasible with four or more spectral bands … spanning the 0.4 to 1.0 um region" (search summary of the same paper). Blue, green, red and NIR is the best broadband set.

**Indian street lighting.**
- The national LED programme (SLNP, run by EESL) had installed about **1.31 crore (13.1 M) LED streetlights as of 7 Nov 2024**, per a Parliament reply (via search summary; [eparlib record](https://eparlib.nic.in/bitstream/123456789/779754/1/AU3430.pdf); [pv-magazine 2019](https://www.pv-magazine-india.com/2019/10/02/india-replaces-one-crore-conventional-streetlights-with-energy-efficient-leds)).
- The sodium share of the replaced fixtures is not given.

**Light-source classification is a remote-sensing topic:**
- SDGSAT-1: accuracy 0.92 for general night lighting and 0.95 for streetlights ([IJDE 2024 via DOAJ](https://doaj.org/article/6aa8e084cfb74cb58a7cabf5960b2c59)).
- UAV classes high-pressure sodium (HPS) / warm LED / cool LED / coloured LED: 95.7% at 350 m, with spectral features dominant ([ECNU record](https://pure.ecnu.edu.cn/en/publications/an-object-oriented-nighttime-light-classification-based-on-light-/)).
- Multispectral UAV LDA: incandescent 83.3%, LED 92.3%, HPS and metal halide 100% ([JARS 14(3) 034528](https://www.spiedigitallibrary.org/journals/journal-of-applied-remote-sensing/volume-14/issue-3/034528/Artificial-illumination-identification-from-an-unmanned-aerial-vehicle/10.1117/1.JRS.14.034528.full)).

**Computational imaging:** "Computational Imaging on the Electric Grid" (Sheinin, Schechner & Kutulakos, CVPR 2017 oral, Best Student Paper; DOI 10.1109/CVPR.2017.254) — [project](https://webee.technion.ac.il/~yoav/research/ACam.html).
- Passive sensing of the AC lighting beat "reveals the type of bulbs in the scene".
- DELIGHT is a database of bulb response functions and chromaticities.
- This is not used for fusion.

**Illumination-aware fusion uses intensity, not spectrum.** Illumination-aware multispectral pedestrian fusion (arXiv 1802.09972) and the "darkness factor" gate in 2608.10680 are both from the audit; the latter is [UV].

**arXiv API checks:**
- `(LED OR sodium) AND (streetlight…) AND (camera OR image OR spectral)` returned 1 hit, a streetlight-management CNN ([2209.08633](https://export.arxiv.org/api/query?search_query=(abs:LED%20OR%20abs:sodium)%20AND%20(abs:streetlight%20OR%20abs:%22street%20light%22%20OR%20abs:%22street%20lighting%22)%20AND%20(abs:camera%20OR%20abs:image%20OR%20abs:spectral))).
- `(NIR…) AND fusion AND segmentation AND physic*` returned only Bishoff 2305.11347.

**Spectral-index channels in RGB-NIR segmentation (old precedent):**
- Freiburg Forest provides "RGB, Depth, NIR, NRG, NDVI, EVI" ([DeepScene](https://deepscene.cs.uni-freiburg.de/)).
- AdapNet, "Adaptive Semantic Segmentation in Adverse Environmental Conditions" (Valada et al., ICRA 2017, pp. 4644–4651).
- CMoDE, "Convoluted Mixture of Deep Experts" (ISER 2016): a gated mixture of modality experts.
- Self-Supervised Model Adaptation (SSMA) (IJCV 2019).
- Sources: [AdapNet repo](https://github.com/DeepSceneSeg/AdapNet); [CMoDE repo](https://github.com/DeepSceneSeg/CMoDE); [IJCV PDF](https://springerlink.bibliotecabuap.elogim.com/content/pdf/10.1007/s11263-019-01188-y.pdf).

**Further index and material precedents:**
- MeCSAFNet (Ramos & Sappa, 2026): 4-channel RGB+NIR and 6-channel input adding NDVI and NDWI ([arXiv 2602.10137](https://arxiv.org/abs/2602.10137)).
- Picon et al. 2022: virtual-NIR indices (IDCS/IDCR) fed to a segmentation net ([Tecnalia PDF](https://dsp.tecnalia.com/bitstreams/c74546e1-c925-4b8a-836d-d09126bf7d8b/download)); numbers from snippet only [UV].
- Salamati et al.: NIR improves material-specific classes ([arXiv 1406.6147](https://arxiv.org/abs/1406.6147)).

**Galway group (metamerism and band selection):**
- Shah et al., "CSNR and JMIM Based Spectral Band Selection for Reducing Metamerism in Urban Driving" (IEEE OJITS 7:1021–1033, 2026; [arXiv 2508.10962](https://arxiv.org/abs/2508.10962)).
  - Uses the contrast signal-to-noise ratio (CSNR) as a band-quality metric.
  - Picks 497, 607 and 895 nm (±27) on H-City V2.
  - Reports dissimilarity gains, with **no segmentation-fusion gating**.
- Li et al., "Band-Selection Stability and Semantic Segmentation Performance: A Study on Hyperspectral City" (WHISPERS 2026; [arXiv 2609.31074](https://arxiv.org/abs/2609.31074)).
  - Six methods, gains "up to 2.01 mIoU", and "performance does not improve monotonically with K".

**SpectraDINO** ([arXiv 2605.02258](https://arxiv.org/abs/2605.02258)).
- One DINOv2-distilled ViT conditioned on NIR/SWIR/LWIR through stems, embeddings and adapters.
- +2.6 mIoU on FMB and +1.4 on SemanticRT.
- No reflectance or physics prior.

### Inferences
- **Illuminant factor: the most novel factor, and the weakest on real data.**
  - IDD-AW was released in 2023, after India had passed 1 crore LED streetlights (2019). Its low-light frames are therefore *probably* LED-dominated with mixed halogen/LED headlights; this is an inference, since IDD-AW gives no lighting information.
  - A real sodium-vs-LED contrast may be rare in the data.
  - **Feasible real-data test (no hardware):** a *within-image* prediction. NIR utility should be higher in headlight-lit near-field regions (halogen is NIR-rich) than in LED-streetlight-lit regions.
  - The illuminant class per region can be estimated from light-source chromaticity together with the NIR/VIS ratio on lit, non-vegetation surfaces (Elvidge shows 4 bands spanning 0.4–1.0 µm identify lamp type).
  - The material confound (vegetation is NIR-bright) must be conditioned out by class.
  - Add the synthetic LED-like vs halogen-like pair in IDD-AW-C.
- **Redundancy objection (likely and serious).** The illuminant (factor 2) and scattering (factor 1) both act on the image *through* measured per-band signal and contrast, which is factor 3. A reviewer will say the four factors collapse to "ΔCNR between NIR and RGB".
  - Pre-empt this with a variance decomposition of OOF utility: partial R² or Shapley over the factor groups. Show that the illuminant and scattering descriptors add predictive power *beyond* measured ΔCNR, or, honestly, that they do not, and that their value is *interpretability and extrapolation*.
  - Shah's CSNR metric (OJITS 2026) is a direct precedent for "contrast SNR decides band value", so ΔCNR alone is not novel.
- **Material factor: circularity risk** (wave1 S2). A class-conditional library prior needs the class.
  - Use library- and camera-curve-derived per-class VIS/NIR contrast only as a *training-time* prior or target regulariser.
  - At inference use class-agnostic local NIR/VIS ratio and NDVI-like indices.
  - Expect reviewers to cite AdapNet, CMoDE and Freiburg Forest (index channels plus gated experts, in adverse conditions, in 2016–17).
  - Optional exact test: H-City V2 hyperspectral cubes (450–950 nm, labelled; wave1) give the *true* per-pixel VIS-vs-NIR contrast after integrating with JAI-like band curves. This is band integration of measured spectra, **not** NIR→RGB colorization, and must be described that way.
- **An NIR-specific failure mode reviewers may raise.** NIR-only light sources, e.g. NIR persistence-of-vision attacks on traffic signs ([2606.30153](https://arxiv.org/abs/2606.30153)), are invisible in RGB. A physics gate with an NIR-only-saturation term should down-weight them. This is a cheap, convincing robustness check [inference].

### Gaps
- I could not verify the share of sodium vs LED lighting at IDD-AW capture sites, or whether IDD-AW night NIR is passive.
- The Elvidge HPS "strongest line at 819 nm" statement is as returned by the fetch tool. I did not re-check whether it refers to the NIR region only.
- No dedicated search for illuminant-aware white-balance or colour-constancy methods that use NIR ("NIR-assisted colour constancy"). These may exist and would be adjacent prior art [unsearched].

---

## Q4. Is there physics-informed (not learned) reliability for sensor fusion in autonomous driving, i.e. a "physics descriptors + learned verifier" pattern reviewers will cite?

### Takeaway
**Partly.**
- **Hand-crafted reliability steering exists.** Seeing Through Fog (CVPR 2020) computes measurement entropy and learns the steering on clear data.
- **Distance- or visibility-aware weighting exists.** SAMFusion weighs modalities "based on distance and visibility"; DGFusion uses depth tokens.
- **Categorical weather gates exist.** 3D-LRF (CVPR 2024) uses a weather classifier.
- **Supervised per-pixel reliability exists.** RAF (ECCV 2026) uses pseudo-labels from cross-modal similarity.
- **Physics models (Koschmieder, Beer–Lambert) are used for *simulation*,** as in FogDrive (2026) and LiDAR fog simulation [mem]. They do **not** feed fusion weights.
- **Not found:** an explicit physical attenuation, visibility or photon-budget model feeding a fusion gate, checked against counterfactual utility.

### Cited Findings
- **Seeing Through Fog** (CVPR 2020): hand-crafted entropy plus learned steering, clear-only training, unseen-weather testing; details and numbers in Q1 ([ar5iv](https://ar5iv.labs.arxiv.org/html/1902.08913)).
- **RAF**, "Reliability-Aware Fusion of Camera, LiDAR, and 4D RADAR for Robust 3D Object Detection in Adverse Weather" (arXiv 2607.04587; comments: "ECCV 2026") — [abs](https://arxiv.org/abs/2607.04587); [HTML](https://arxiv.org/html/2607.04587).
  - "explicitly supervises per-pixel reliability estimation".
  - "Since no reliability ground truth exists, we construct image-level cleanliness labels (Clean, Mixed, Noisy)".
  - "The similarity scores … serve as pseudo reliability labels at projected pixel locations".
  - "gating without proper supervision harms detection".
  - Gains up to +6.5 AP_BEV and +7.4 AP_3D on K-Radar/VoD.
  - No physics model and no held-out-weather protocol.
- **SAMFusion**, "Sensor-Adaptive Multimodal Fusion for 3D Object Detection in Adverse Weather" ([arXiv 2508.16408](https://arxiv.org/abs/2508.16408); ECCV 2024 [mem]).
  - "transformer decoder that weighs modalities based on distance and visibility".
  - Uses an NIR gated camera.
  - +17.2 AP for long-range pedestrians in fog.
  - The weighting is learned attention, not a physics formula, as far as the abstract shows.
- **3D-LRF** (CVPR 2024): weather-classifier-conditioned radar gating (Q1). Follow-ups: L4DR ([2408.03677](https://arxiv.org/html/2408.03677v6)) and DLR-Fusion (ICCV 2025, [CVF](https://openaccess.thecvf.com/content/ICCV2025/html/Chae_Doppler-Aware_LiDAR-RADAR_Fusion_for_Weather-Robust_3D_Detection_ICCV_2025_paper.html)).
- **Huang et al.** ([arXiv 2402.02738](https://arxiv.org/abs/2402.02738)): "flexibly weighted fusing features from LiDAR and camera sources to adapt to varying weather scenarios" on corrupted KITTI. Most fusion models lean on LiDAR (search summary).
- **FogDrive** (2607.22698): Koschmieder (camera) and Beer–Lambert (LiDAR) fog, but only for *data*.
- **Gated imaging + LiDAR** with uncertainty-based adaptive weighting (MDPI *Sensors* 2026): learned uncertainty, not physics ([MDPI](https://www.mdpi.com/1424-8220/26/12/3728/htm)).
- **Image-based visibility estimation** with Koschmieder's law, using LiDAR distances, appears in a patent, not a fusion network ([USPTO 9536173](https://image-ppubs.uspto.gov/dirsearch-public/print/downloadPdf/9536173)).
- **DCDA** (ECCV 2026) explicitly avoids "explicit weather modeling" yet generalizes to unseen weather ([HTML](https://arxiv.org/html/2607.01983)).
  - This is a counter-position reviewers will raise: "weather-agnostic learning already handles open weather".

### Inferences
- **Reviewers will cite** Seeing Through Fog, SAMFusion, 3D-LRF, RAF, DCDA, CAFuser and DGFusion as the "reliability-aware or condition-aware fusion under weather" family.
- **A′ differs on three axes:**
  - (i) The descriptors are *derived from wavelength physics per band*, not entropy, learned visibility or categorical weather.
  - (ii) The gate is *verified and supervised against cross-fitted counterfactual utility*, not cross-modal similarity pseudo-labels (RAF) or task loss.
  - (iii) The task is dense segmentation, with an explicit held-out-condition comparison.
- **RAF is a new must-cite for C1.** It is the first ECCV-level "per-pixel supervised reliability gate" in fusion. Its quote "gating without proper supervision harms detection" is useful *motivation* for A′.
- **DCDA's counter-position** must be answered in the results. Include a weather-agnostic learned baseline (modality dropout plus a content gate) under the same LOCO protocol, and show where it over-trusts NIR.

### Gaps
- LiDAR fog and snowfall physics simulators (Hahner et al., ICCV 2021 / CVPR 2022) and multi-sensor corruption suites (MultiCorrupt, Robo3D) are cited from memory [mem]. They were not re-fetched.
- SAMFusion's full text was not read, so whether its "visibility" weighting uses any physical model is [UV].

---

## Q5. Component-by-component closest prior work, and what remains open

### Takeaway
Every *component* of A′ has a neighbour. The **combination** has none: wavelength-physics descriptors, a gate structurally constrained by that physics, cross-fitted dense counterfactual utility used both as target and as falsifier, and a LOCO plus cross-camera evaluation of RGB–NIR segmentation gates. The most defensible novelty is the *falsifiable quantitative link* between spectral physics and a trained network's marginal NIR utility.

### Cited Findings
| A′ component | Closest prior works (title, venue, year, arXiv id) | Verdict | What remains open |
|---|---|---|---|
| **F1** Scattering regime (haze λ^−γ vs Mie fog γ≈0) | Kim visibility model ([Radioengineering 2010](https://www.radioeng.cz/fulltexts/2010/10_02_223_227.pdf)); HSVF, IEEE TMM 2025, [2507.03893](https://arxiv.org/abs/2507.03893) (single β, weak Mie λ-dependence); Bishoff et al., SPIE DCS 2023 [UV], [2305.11347](https://arxiv.org/abs/2305.11347) (reduced NIR fog/haze); Pinchon et al., AMAA 2018 ([HAL](https://hal.archives-ouvertes.fr/hal-01406023)); AWFusion, [2402.02090](https://arxiv.org/abs/2402.02090) (transmission-aware image fusion) | Physics textbook; heuristic use exists | Per-pixel implied-q̂ descriptor in a segmentation gate. Prediction "utility ≈ 0 at q = 0" tested against counterfactual utility with a matched spectral/flat pair |
| **F2** Illuminant spectrum at night (HPS/MH 819 nm, halogen NIR-rich, LED none) | Elvidge et al., *Sensors* 2010 ([PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC3274255/)); ACam, CVPR 2017 ([project](https://webee.technion.ac.il/~yoav/research/ACam.html)); remote-sensing lamp classification (IJDE 2024, TGRS 2025, JARS 2020); illumination-*intensity*-aware RGB-T fusion (1802.09972) | **Not found in perception fusion** | Illuminant-type cue for NIR trust; within-image halogen-vs-LED test; LED/halogen synthetic pair. Must show it adds beyond ΔCNR |
| **F3** Per-band photon budget / Poisson–Gaussian SNR | Foi et al., TIP 2008 (estimator); Shah et al., OJITS 2026, [2508.10962](https://arxiv.org/abs/2508.10962) (CSNR band quality); SRD, [2606.01173](https://arxiv.org/abs/2606.01173) (Fourier energy descriptors); Seeing Through Fog, CVPR 2020, [1902.08913](https://arxiv.org/abs/1902.08913) (entropy); Deno-IF, NeurIPS 2025 (noise-aware image fusion; audit) | Partially done (as cue or metric) | Per-band PG σ and ΔCNR as *monotone* gate inputs for RGB–NIR segmentation, with extrapolation to held-out low light |
| **F4** Material VIS-vs-NIR contrast (red edge, fabrics, paints) | Salamati et al., [1406.6147](https://arxiv.org/abs/1406.6147); Brown & Süsstrunk, CVPR 2011 (wave1); AdapNet, ICRA 2017, and CMoDE, ISER 2016 (NDVI/EVI channels + gated experts); MeCSAFNet, 2026, [2602.10137](https://arxiv.org/abs/2602.10137); Shah, OJITS 2026, [2508.10962](https://arxiv.org/abs/2508.10962), and Li, WHISPERS 2026, [2609.31074](https://arxiv.org/abs/2609.31074) (band selection on H-City); MCubeS, CVPR 2022 | Index inputs done; library prior not found | Library + camera-curve contrast prior predicting per-class and per-pixel NIR utility (rank-correlation test). Exact check on H-City V2 spectra |
| **F5** Gate supervised by cross-fitted dense counterfactual utility | TIER-MoE, [2607.27289](https://arxiv.org/abs/2607.27289); Boundary-Aligned Contribution Routing, [2608.15261](https://arxiv.org/abs/2608.15261); GAUGE, [2608.05608](https://arxiv.org/abs/2608.05608) (all 2026, sample-level; audit); **RAF**, ECCV 2026, [2607.04587](https://arxiv.org/abs/2607.04587) (per-pixel reliability supervised by cross-modal-similarity pseudo-labels); RSFNet, [2306.10364](https://arxiv.org/abs/2306.10364) (saliency pseudo-labels) | Partially done | Dense *counterfactual-utility* target (not similarity or saliency) for an RGB-X *segmentation* gate; utility used also as a *falsification instrument* for physics predictions |
| **F6** Headline: physics gates generalize to unseen conditions where learned gates fail (LOCO, cross-dataset) | Seeing Through Fog, CVPR 2020, [1902.08913](https://arxiv.org/abs/1902.08913) (hand-crafted entropy, clear-only training, unseen weather); DCDA, ECCV 2026, [2607.01983](https://arxiv.org/abs/2607.01983) (type-, severity- and type+severity-open protocols, LiDAR–radar); 3D-LRF, CVPR 2024 (weather-classifier gate); CAFuser, RA-L 2025, [2410.10791](https://arxiv.org/abs/2410.10791) (condition token, no held-out metric); SRD, [2606.01173](https://arxiv.org/abs/2606.01173) (unseen corruptions, cross-dataset, no descriptor-vs-learned comparison); DSSS, [2505.07050](https://arxiv.org/abs/2505.07050) (RGB-D DG segmentation) | **Open for RGB-X segmentation; antecedents exist** | LOCO on IDD-AW, IDD-AW → MCubeS gate transplant with camera re-parameterisation; physics-constrained vs learned vs condition-token vs physics-input-MLP gates on held-out conditions |
| **F7** IDD-AW-C wavelength-correct suite with spectral/flat control | Bishoff, [2305.11347](https://arxiv.org/abs/2305.11347) (overhead, heuristic); SynFog, [2403.17094](https://arxiv.org/abs/2403.17094) and FogDrive, [2607.22698](https://arxiv.org/abs/2607.22698) (visible-only physics); sensor-failure benchmark, CVPRW 2025, [2503.18445](https://arxiv.org/abs/2503.18445); AWMM-100K, [2402.02090](https://arxiv.org/abs/2402.02090) | Partially done | Physically parameterised per-band driving suite, matched spectral/flat and LED/halogen pairs, validated against real conditions |

### Inferences
- **The scientific claim is open:** "a four-factor spectral-physics model predicts where a trained RGB–NIR segmenter benefits from NIR, and this prediction survives counterfactual falsification and condition shift". It is the strongest A* asset and it moves the paper out of the crowded "yet another reliability gate" space.
- **The method claims are not open on their own:** "physics gate", "utility supervision" and "unseen-weather robustness" each have a precedent. A′ must lead with the scientific claim and present the gate as the *instrument* that tests it.

### Gaps
- F4's "fabrics, paints" spectral-library coverage was not checked: which USGS/ECOSTRESS entries map to IDD-AW classes [UV].
- JAI FS-3200D per-channel QE curves remain unpublished (blueprint). Camera-curve integration for F4 and F1 uses approximate band centres [UV].

---

## Q6. Revised novelty scores, strongest thesis and framing, 3 safe claims, and the decisive experiment

### Takeaway
- **Revised A\*: 6/10** (from 5).
  - It rises to about 7 if the decisive experiment produces a clean, pre-registered result: physics-constrained gates beat learned, condition-token and physics-input-MLP gates on held-out conditions, and mis-specified physics erases the advantage.
  - It falls to about 4 if LOCO differences sit inside seed noise; the paper then becomes an analysis paper.
- **Revised Q1: 7.5/10** (from 7). The falsifiable physics account plus IDD-AW RGB-X benchmark plus IDD-AW-C is strong journal material even with a modest generalization result.
- **Is the novelty genuinely A\*-level?** The reframe makes it *eligible*, not *sufficient*. The deciding factor is evidence quality, and two of the four physics factors (haze, illuminant) can only be tested synthetically or through weak real proxies on IDD-AW.

### Cited Findings
- **Venue windows** (all [EST], from `venues_figures_and_paperC.md`):
  - ICCV 2027: early March 2027.
  - ACM MM 2027: about Apr 1, 2027. Public reviews; arXiv not treated as prior publication.
  - NeurIPS 2027: early to mid May 2027.
  - AAAI-28: late July 2027.
- **Antecedents that cap the score:** Seeing Through Fog ([1902.08913](https://arxiv.org/abs/1902.08913)), DCDA ([2607.01983](https://arxiv.org/abs/2607.01983)), Bishoff ([2305.11347](https://arxiv.org/abs/2305.11347)), RAF ([2607.04587](https://arxiv.org/abs/2607.04587)), SRD ([2606.01173](https://arxiv.org/abs/2606.01173)), 3D-LRF (CVPR 2024), CAFuser ([2410.10791](https://arxiv.org/abs/2410.10791)), plus the C1 trio from the audit.
- **Small real-data effect sizes:** the IDD-AW per-condition fused gains are +4 (rain), +1 (fog), +1 (low light) and 0 (snow) ([IDD-AW HTML](https://arxiv.org/html/2311.14459)).
- **Possible haze in Indian "fog" (context only):** fine-mode aerosol dominance in winter and post-monsoon over the Indo-Gangetic Plain AERONET sites. "Fine mode fractions or a mixture of modes during winter and postmonsoon" ([WCRP 2011 abstract, Kedia](https://www.wcrp-climate.org/conference2011/abstracts/C7/Kedia_C7_M20B.pdf)). So IDD-AW "fog" frames *may* contain haze with q > 0; whether they do is unknown.

### Inferences

**Strongest one-sentence thesis**
> NIR's marginal value to an RGB–NIR segmenter is not a property of the training conditions but a predictable function of per-band photon budget, wavelength-dependent scattering, illuminant spectrum and material reflectance. Gates constrained to this physics transfer to unseen weather and cameras, whereas learned and condition-token gates over-trust NIR exactly where physics says it carries no information.

**Best title**
- Recommended: **"When Does Near-Infrared Help? A Falsifiable Spectral-Physics Account of Modality Utility for Robust RGB–NIR Segmentation"**.
- Alternative: "Physics, Not Weather Labels: Transferable RGB–NIR Fusion Gates from Spectral First Principles".
- Avoid "Learning Where to Trust…" (collides with 2610.36672).

**Abstract-level claim (template; fill X/Y only after results)**
> We measure, for every pixel, how much NIR changes a trained segmenter's loss (cross-fitted counterfactual utility). We show that a four-factor spectral-physics model explains a substantial share of it (partial R² = X), and confirm its pre-registered predictions: utility ≈ 0 under wavelength-flat fog, higher under spectral than flat haze at matched visibility, and higher under halogen than LED light. A gate constrained to this physics, trained on three IDD-AW conditions, cuts negative transfer on the held-out fourth by Y% relative to learned, condition-token and physics-input-MLP gates. It transfers to MCubeS by re-parameterising the camera, while a mis-specified-physics control does not.

**The 3 claims that are safe now (each with "to our knowledge" and the stated citations)**
1. **A falsifiable, per-pixel spectral-physics model of NIR's marginal utility in RGB–NIR segmentation,** verified against cross-fitted dense counterfactual utility.
   - Prediction set: Mie-fog ≈ 0; spectral > flat haze; halogen > LED; SNR monotonicity; material ordering.
   - Cite: Seeing Through Fog (entropy cue), SRD (Fourier descriptors), Bishoff (heuristic NIR fog), Shah (CSNR), TIER-MoE / 2608.15261 / GAUGE / RAF (utility or reliability supervision).
2. **The first held-out-condition (LOCO) and cross-camera evaluation protocol for RGB-X segmentation fusion gates,** comparing physics-constrained, learned, condition-token and physics-input-MLP gates on dense utility metrics.
   - Cite DCDA as the LiDAR–radar detection precedent, and Seeing Through Fog as clear→adverse.
3. **A physically parameterised per-band RGB–NIR driving corruption suite (IDD-AW-C) with matched spectral/flat haze and LED/halogen low-light control pairs,** used as a falsification instrument and validated against real IDD-AW conditions.
   - Cite Bishoff (overhead heuristic), SynFog/FogDrive (visible-only), the sensor-failure benchmark and AWMM-100K.

**Not safe until shown:**
- "physics gates generalize better"
- "illuminant-aware fusion works on real data"
- any IDD-AW SOTA (must beat 69.8 val; audit)

**Must not claim (in addition to the audit list):**
- First fusion robust to *unseen* weather (Seeing Through Fog).
- First held-out-weather protocol for fusion (DCDA).
- First wavelength-aware NIR corruption (Bishoff).
- First supervised per-pixel reliability gate (RAF; RSFNet).
- First descriptor or hand-crafted reliability cue (Seeing Through Fog, SRD, illumination-aware fusion).
- That NIR "penetrates" fog.

**Decisive experiment: "held-out-condition gate transplant" (IDD-AW 4-fold LOCO, plus IDD-AW-C control pairs)**

Design choice: make gates *swappable on a frozen segmenter*. This isolates the gate and makes the experiment cheap.

Per fold (held-out condition c ∈ {rain, fog, snow, low light}):

1. **Host model.** Train one RGB–NIR host (MiT-B2 CMNeXt/StitchFusion-style, NIR modality dropout) on the 3 seen conditions; 3 seeds.
2. **Utility targets.** Compute *cross-fitted* dense utility on seen-condition training pixels with K = 2 sequence-grouped fold models:
   - u(x) = CE(f(RGB, ∅))(x) − CE(f(RGB, NIR))(x)
   - Add a seed-noise null.
3. **Gate.** Use a late, logit-level gate: ŷ = g(x)·f(RGB, NIR) + (1 − g(x))·f(RGB, ∅). The Bayes-optimal gate is 1[E[u|x] > 0], so g targets sign and magnitude of u.
4. **Gate arms** (all on the same frozen host; all except G0, G1 and G6 trained on OOF u):

   | Arm | Description |
   |---|---|
   | G0 | Always fuse (g ≡ 1) |
   | G1 | Learned content gate (image features, task loss) |
   | G2 | Condition-token gate (CAFuser-style categorical over the 3 seen conditions; 3D-LRF-style) |
   | G3 | Unconstrained MLP on [features + physics descriptors φ] |
   | G4 | **Physics gate**: sign-constrained logistic or monotone lattice on φ = {ΔlogCNR, per-band σ, q̂, illuminant NIR/VIS ratio, material-contrast prior, NIR-only saturation} |
   | G5 | **Mis-specified physics**: G4's form with per-band σ shuffled, λ ordering swapped and the q̂ sign flipped |
   | G6 | Oracle (held-out true u), upper bound |

5. **Endpoints on the held-out condition's val images:**
   - Primary: Spearman ρ(g, u_test) and sign-AUROC.
   - Null-calibrated pixel NTR.
   - Secondary: mIoU / SmIoU, with sequence-cluster bootstrap CIs.

**Pre-registered predictions (pass = physics account supported)**
- **P-a (held-out fog):** median |u| is within the seed-noise null for most pixels, and NTR(G4) < NTR(G1, G2).
- **P-b (held-out low light):** ρ(G4) − ρ(G1) ≥ 0.1, and the gap grows with estimated σ (darkness bins).
- **P-c (IDD-AW-C, matched V):** true u and mean g(G4) are higher under spectral than flat haze, increasing with severity. G1–G3 do not separate the pair.
- **P-d (LED-like vs halogen-like low light):** u and g(G4) drop under LED-like. Also run the within-image real-data check: headlight-lit regions vs LED-streetlight-lit regions on real IDD-AW low light.
- **P-e:** G5 ≈ G1. This shows the gain is physics content, not input capacity. G4 > G3 shows it is the physics *structure*, not just the inputs.
- **Cross-camera:** transplant G4 (re-parameterised to the FLIR NIR band) and G1 into an MCubeS RGB-N host trained with dropout. Compare ρ(g, u_MCubeS) and NTR.

**Kill or go criteria**
- **Week 2–3 pre-test, using the existing all-condition model:** if the physics factors explain less than about 10% of OOF-utility variance (partial R²), or the q̂ distribution on IDD-AW fog is indistinguishable between "fog" and clear-ish frames *and* P-c fails on IDD-AW-C, then drop the generalization headline and retreat to the Q1 analysis paper.
- **Main experiment:** if G4 does not beat G3 on held-out ρ and NTR in at least 3 of 4 folds with bootstrap CIs excluding 0, the A* framing fails.

**Compute estimate (my estimate, [UV])**
- About 4 folds × 3 seeds × (1 host + 2 cross-fit models) = 36 MiT-B2 runs. At roughly 8–12 GPU-h each that is about 290–430 GPU-h.
- Using cross-fit models for 1 seed only reduces this to about 20 runs, roughly 160–240 GPU-h.
- Gate heads on frozen hosts take minutes each.
- This fits one 24 GB GPU in about 2–3 weeks and replaces part of the blueprint's 650–800 GPU-h ablation grid. It does not add to it.

**Venue framing**
- **ACM MM 2027 (~Apr 1): best fit.** Multimodal audience, physics-plus-benchmark story, public reviews reward careful falsification.
- **ICCV 2027 (early Mar): feasible only if** the pre-test passes by mid-December and LOCO finishes by mid-February. Frame it as physics-based vision plus robustness.
- **NeurIPS 2027 (May):** only with a theory section. Possible content:
  - The Bayes-optimal gate is monotone in conditional utility.
  - Under covariate shift in physics descriptors, where the physics-implied monotone structure still holds in the target domain, a shape-constrained gate has bounded extrapolation error, but an unconstrained gate does not. This is my conjecture, unverified.
- **AAAI-28 (late Jul):** fallback with full results.

### Gaps
- The 10% partial-R² kill threshold and the ρ ≥ 0.1 margin are my heuristics, not literature values.
- Whether IDD-AW "fog" contains haze (q > 0) is unknown until q̂ is measured. IDD-AW gives no locations or months per condition.
- No labelled real RGB–NIR haze set is available. VNHD needs an author request.
- MCubeS has no adverse conditions, so cross-dataset transfer tests only the SNR and material factors.
- None of the scores were calibrated against actual 2026 review outcomes. They are judgment calls based on the prior-art density documented above.
