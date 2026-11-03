## NIR-assisted low-light restoration and perception: prior-art map and novelty check (as of 2026-11-03)

**Search was cut short.** The web-search budget ran out before I finished. The last query I could not run was "2025–26 RGB-NIR low-light detection papers". Before committing to a topic, run one more sweep of CVPR 2026, ECCV 2026 and arXiv for "NIR" + "detection" / "segmentation" + "low-light".

### Corrections to the brief
- **DVN** is AAAI 2022, not CVPR ([AAAI](https://ojs.aaai.org/index.php/AAAI/article/view/19995), arXiv 2303.06834).
- **FANVID** is not Niu et al.'s dataset.
  - FANVID comes from Gao et al., *Object-Aware NIR-to-Visible Translation*, ECCV 2024 ([ECVA](https://www.ecva.net/papers/eccv_2024/papers_ECCV/html/3338_ECCV_2024_paper.php)). It has 5,144 aligned **image** pairs in the 700–800 nm band and was built for colorization.
  - Niu et al., ICCV 2023 released **FMSVD**: 64 scenes, 41,363 frames, a JAI FS-3200T 3-CMOS prism camera, with night scenes lit by **co-located 850 nm LEDs** ([CVF](https://openaccess.thecvf.com/content/ICCV2023/html/Niu_NIR-assisted_Video_Enhancement_via_Unpaired_24-hour_Data_ICCV_2023_paper.html)).
- **Real-NAID / SFM** was published in IEEE TMM 2025 ([arXiv 2404.08514](https://arxiv.org/html/2404.08514v4)).
- **IDD-AW** was captured with a JAI FS-3200D 2-CMOS prism camera (pixel-aligned; VIS 400–670 nm, NIR 740–1000 nm). The paper reports **no active NIR illuminator**, so its night NIR is passive ([arXiv 2311.14459](https://arxiv.org/abs/2311.14459)).
- **The thesis finding is already in the literature**, which helps with framing:
  - Restoration can lower detector accuracy: Sun et al., NeurIPS 2022 ([link](https://neurips.cc/virtual/2022/poster/54700)).
  - The effect of low-light enhancement on detection is "inconsistent and can sometimes be harmful": Wu et al., [arXiv 2409.14461](https://arxiv.org/abs/2409.14461).
  - Turgeman & Tirer, ICLR 2026 ([arXiv 2512.21315](https://arxiv.org/abs/2512.21315)) formalise the data-processing-inequality argument. They also prove that, with finite training data, some preprocessing *can* help. Cite this, or a reviewer will.

### (1) State of the art

| Method | Venue / year | Data | Best reported numbers | How it handles RGB–NIR inconsistency |
|---|---|---|---|---|
| Scale Map ([Yan](https://openaccess.thecvf.com/content_iccv_2013/html/Yan_Cross-Field_Joint_Image_2013_ICCV_paper.html)) | ICCV 2013 | NIR / flash pairs | DVD σ=4: 21.02 / 0.62 (as a baseline in DVN's table) | Derivative-level scale map (statistical, not physical) |
| Deep-red flash ([Xiong](https://openaccess.thecvf.com/content/CVPR2021/html/Xiong_Seeing_in_Extra_Darkness_Using_a_Deep-Red_Flash_CVPR_2021_paper.html)) | CVPR 2021 | 660 nm flash prototype | – | Guided-filter CNN |
| DVN | AAAI 2022 | DVD: filter-switched captures, synthetic low light, plus 10 real noisy scenes | σ=2/4/6/8: 31.50 / 29.62 / 28.26 / 26.98 dB; SSIM 0.940 at σ=4 | Deep Inconsistency Prior: Sobel-edge agreement between predicted deep structure maps suppresses NIR-only edges |
| Wan et al. | IEEE TMM 2023 | Own RGB-NIR pairs | *unverified* | Separates structure from colour |
| NVEU (Niu) | ICCV 2023 | FMSVD, unpaired day→night | No-reference metrics only (PI / NIQE / human study) | Implicit. Its relighting models only **geometry** (depth falloff, incidence angle), not spectrum |
| SANet / SGDFormer ([2404.00349](https://arxiv.org/abs/2404.00349v1)) | CVPR 2023 / Information Fusion 2025 | Unaligned stereo RGB-NIR | – | Cross-attention correspondence plus spatially varying fusion |
| RFFNet | ACM MM 2024 | – | *unverified* | "Robust and flexible fusion" (details not checked) |
| NAID / SFM | TMM 2025 | Real-NAID: 100 static scenes × 3 ISO levels; surveillance camera with a **built-in NIR illuminator**; clean target = ISO 600 long exposure | NIR-Restormer low / mid / high noise: **26.22 / 25.51 / 24.76 dB**; LPIPS 0.265 / 0.293 / 0.315 | Global and local modulation weights. Gain over plain summation is only about **0.23 dB** |
| FCENet ([2412.16645](https://arxiv.org/html/2412.16645v2)) | CVPR 2025 | DVD; EPFL (IVRG) | DVD σ=2/4/6: **32.43 / 30.37 / 28.78 dB**. **Not tested on Real-NAID** | Frequency-correlation prior with dynamic frequency selection |
| Kim & Baek ([2411.18025](https://arxiv.org/html/2411.18025v2)) | CVPR 2025 | Pixel-aligned RGB-NIR stereo robot dataset, about 80k frames, **NIR LED bar** | Frozen YOLO mAP 0.756 (RGB) → **0.809** (fused). Stereo RMSE 8.94 → 6.75 | Learned spatially varying weights on the HSV brightness channel, plus a guided filter |
| FD-INR ([2604.16800](https://arxiv.org/html/2604.16800)) | arXiv, April 2026 | Real DVD subset (10 scenes), per-instance fitting | NIQE 2.90 only. Claims detection gains but shows none | Learnable offset for the brightness (DC) mismatch, plus a zero-mean high-frequency branch |
| Niu et al., 3D-aware ([2607.29684](https://arxiv.org/html/2607.29684)) | ACM MM 2026 | Mitsuba synthetic plus real scenes (850 nm LED), static only | At 1/200 light: 19.68 dB / 0.703 vs Restormer 14.43 dB | Needs **no clean RGB**. Uses Gumbel colour codes for the one-to-many NIR→RGB mapping |
| NIR-guided dehazing | Schaul, ICIP 2013; HSVF, TMM (accepted), [2507.03893](https://arxiv.org/abs/2507.03893) | HSVF adds a pixel-aligned VIS-NIR haze dataset (JAI FS-3200D) | *unverified* | Semantic stream plus NIR visual stream |
| NIR-guided deraining / desnowing | – | – | **Nothing found** | – |
| IDD-AW ([WACV 2024](https://openaccess.thecvf.com/content/WACV2024/html/Shaik_IDD-AW_A_Benchmark_for_Safe_and_Robust_Segmentation_of_Drive_WACV_2024_paper.html)) | WACV 2024 | 5k pairs; InternImage-b with RGB and NIR stacked as input channels | mIoU RGB / NIR / stack: low light **62 / 57 / 63**; rain 62 / 61 / 66; fog 64 / 58 / 65; snow 53 / 51 / 53; all 64 / 61 / 67 | Naive stacking |
| TRNT / AUNet ([2602.06363](https://arxiv.org/html/2602.06363)) | AAAI 2026 | 8,281 RGB-NIR-TIR pedestrian triplets, captured 08:00–22:00 | AP50 RGB 77.0, NIR 72.6, **RGB+NIR 91.6** | Uncertainty router across modalities |

**Caveats on the table**
- **DVD numbers across papers are not comparable.** The NAID paper reports DVN at σ=4 as 29.69 / 0.906; DVN's own paper says 29.62 / 0.940.
- **No NIR-guided restorer has been evaluated by downstream detection or segmentation on real night data.** That covers DVN, SFM, FCENet and NVEU. Kim & Baek is the only near-exception, and it uses actively lit robot scenes.
- **Nearly every NIR-guided low-light dataset relies on active NIR light**: Real-NAID, FMSVD, Kim & Baek, and Niu MM'26. DVD's lighting is unclear. **Passive night NIR, as in IDD-AW, has not been studied as a restoration setting.**
- **Light-source physics:**
  - High-pressure sodium and metal-halide lamps have strong 819 nm lines ([Elvidge, Sensors 2010](https://pmc.ncbi.nlm.nih.gov/articles/PMC3274255)). New York City's night radiance peaks at the 818 nm sodium line (Sensors 2016).
  - White LEDs are blue chips plus phosphor. That their NIR output is negligible is *my inference, not measured in the sources*.
  - NIR/RGB brightness ratios reveal the illuminant (Fredembach & Süsstrunk 2009, [EPFL](https://infoscience.epfl.ch/record/133341)); a multi-band NIR colour-constancy paper extends this (IEEE Access 2024).

### (2) Verdict per candidate

| Candidate | Closest prior work | What is still open | A* score | Q1 score | Main reviewer risk |
|---|---|---|---|---|---|
| **R1. Task-preserving NIR-guided enhancement** | Kim & Baek CVPR 2025; task-driven fusion (TarDAL CVPR 2022, SeAFusion, TDFusion CVPR 2025 [2412.03240](https://arxiv.org/abs/2412.03240)), all thermal; CDDFuse CVPR 2023 (invertible blocks for "lossless" fusion); Sun NeurIPS 2022; WSA-YOLO (invertible enhancement for detection, venue unverified) | A NIR-guided restorer with a no-regret guarantee (it can always fall back to the RGB input), validated by detection and segmentation on real night data | 5 | 7 | "TarDAL for NIR." Invertibility guarantees information is kept, not that it is usable, and the identity map is trivially invertible. The guarantee also only covers retrained detectors |
| **R2. Learning from unreliable references** | Niu ICCV 2023 (unpaired); Niu MM 2026 (no clean RGB, static, per-scene); FD-INR; Self-MM ([2006.13714](https://arxiv.org/pdf/2006.13714)); imperfect-supervision de-weathering (Liu et al., AAAI 2024, [2310.14958](https://arxiv.org/pdf/2310.14958)); misalignment-robust loss FDL (CVPR 2024); GT-Mean loss (ICCV 2025) | A feed-forward method for **dynamic** scenes that uses the clean, aligned NIR frame to score how reliable each pixel of a misaligned or noisy target is | 5 | 7 | Reads as AAAI'24 + NAID combined. The **same HIT group** authored both NAID and the de-weathering paper, so scoop risk is high. Needs a new real dataset with dynamic scenes |
| **R3. Spectrally grounded structure transfer** | Scale Map; DVN's prior; SFM; FCENet; EPFL material and illuminant work (Salamati, Fredembach); object-aware NIR→VIS translation (ECCV 2024, segmentation prior on reflectance) | No restorer uses a physical VIS-vs-NIR reflectance model to decide where NIR edges are trustworthy | 4 | 6 | One NIR band cannot pin down reflectance, so the "physics" collapses into learned material classes. Learned gates are already near the ceiling (SFM is +0.23 dB over plain summation) |
| **R4. Illuminant-spectrum-aware NIR guidance** | NVEU relighting (geometry only); Fredembach 2009; IEEE Access 2024; Elvidge 2010; [2606.14248](https://arxiv.org/pdf/2606.14248) | **Not addressed by any restoration or perception method found**, and no dataset labels light-source types | 6 | 7 | Niche; illuminant effects are confounded with other factors; needs spectral ground truth (for example a spectrometer) |
| **R5. "When does NIR help?" benchmark** | IDD-AW (+1 mIoU from NIR in low light); Kim & Baek (+5.3 mAP); TRNT (+14.6 AP50 with a dedicated network); Wu et al. 2409.14461; Sun NeurIPS 2022; Turgeman & Tirer ICLR 2026 | No cross-evaluation of NIR-guided enhancers vs RGB-only enhancers vs fusion detectors under the thesis protocols (raw→raw, raw→enhanced, enhanced→enhanced), split by illumination | 4 | 7–8 | "Benchmark without a method." Labels are thin: IDD-AW has segmentation; TRNT has pedestrian boxes; Kim & Baek's release lists no labels |
| **R6a. NIR guidance for rain and snow** | No NIR-guided deraining or desnowing found. IDD-AW shows NIR helps in rain (+4 mIoU) but not snow (0) | Physics: large droplets and flakes scatter roughly independent of wavelength, while haze scatters less in NIR. Restoration or task-driven fusion is open | 5 | 7 | No clean targets exist, so evaluation must be through downstream tasks |
| **R6b. Noisy-guide (passive NIR) guided restoration** | DVN, SFM, FCENet and FD-INR all assume clean NIR; Self-MM is the only partial exception | Restoring both modalities jointly when RGB and NIR are both photon-starved | 6 | 7 | Needs real passive-NIR night pairs. IDD-AW has no clean targets |

### (3) Recommended directions

**For A*: "Passive-NIR night" — illuminant-aware, no-regret NIR guidance, validated by downstream tasks (R4 + R6b + R1).**
- **Core claim:** existing NIR-guided restoration is benchmarked almost entirely under active NIR light, so how much NIR actually helps at night is overestimated. The real value is limited by the NIR photon budget, which depends on the light source (sodium or halogen vs LED vs headlights vs IR illuminator).
- **Method:**
  - Estimate a per-region NIR reliability (illuminant) map from NIR/RGB ratio statistics, grounded in Fredembach 2009 and Elvidge 2010.
  - Restore both RGB and NIR jointly.
  - Inject NIR as a gated residual. When the gate is zero the output equals RGB-only enhancement, which gives the no-regret property.
  - Fine-tune with segmentation and detection losses.
- **Evaluation:** report on three fronts at once.
  - Restoration fidelity on Real-NAID and DVD.
  - Segmentation on IDD-AW low light.
  - Detection on TRNT night.
  - A stratified analysis showing DVN, SFM and FCENet degrade under passive or LED-lit NIR while the new method does not.
- **Numbers needed:**
  - Real-NAID: at least +0.3–0.5 dB over NIR-Restormer (high noise ≥25.2 dB; average ≥25.8) and LPIPS ≤0.29. FCENet must also be run on Real-NAID as a baseline.
  - DVD (FCENet protocol): σ=4 ≥30.6 dB.
  - IDD-AW low light with the same InternImage-b backbone: ≥65–66 mIoU, vs 62 (RGB) and 63 (stacking), and never below RGB-only in any condition.
  - TRNT: at least +10 AP50 over RGB-only (77.0), while staying detector-agnostic.
- **Risk:** moderate to high. It needs a small new passive-night capture with labelled light sources to convince A* reviewers.

**For a Q1 journal (Information Fusion, TCSVT or Pattern Recognition): R5 with a light R1 component.** Title idea: "Does NIR guidance make low-light enhancement useful for machines?"
- **Fit with the thesis:** it reuses the thesis protocols and the published negative result, that RGB-only enhancers add no information. The new test is whether NIR-guided enhancers (DVN, SFM, FCENet, NVEU; Kim & Baek-style fusion), retrained on Real-NAID, DVD or FMSVD, reverse it.
- **Study design:**
  - Real-data evaluation on IDD-AW (segmentation, split by weather) and TRNT (detection, split by day / night / extreme low light).
  - Compare against RGB-only enhancers and stacking or AUNet-style fusion detectors.
  - Add a simple no-regret gated-residual baseline as the method contribution.
  - Analyse by condition: active vs passive NIR, rain vs snow, and Turgeman & Tirer's finite-sample regime.
- **Results that would make it a strong paper:**
  - RGB-only enhancers lose mAP or mIoU on raw-trained models (consistent with the thesis).
  - NIR-guided enhancement gives a clear gain: ≥ +2–5 mIoU on IDD-AW low light, rain and fog, and ≥ +5 AP50 on TRNT night, even with the detector frozen.
  - The no-regret baseline is never worse than raw input by more than about 0.5 points in any condition, and comes within about 1–2 points of dedicated fusion detectors (IDD-AW stacking 67 overall; AUNet 91.6 AP50).
- **Risk:** low to moderate, and it is the most feasible option for an MS student.

**Unverified items:** RFFNet's method and numbers; Wan TMM 2023 numbers; HSVF and MMDehazeNet numbers; WSA-YOLO's venue; TRNT's NIR band and whether it used active light; that white LEDs emit negligible NIR (my inference); the Kim & Baek label release; whether IDD-AW truly had no active illumination (the paper simply does not mention one).

**Files:** text extracted from the IDD-AW and NVEU PDFs is in `/tmp/claude-0/-home-user/62eeef81-e163-5d0c-ab02-e8bd308466a5/scratchpad/dl/`.
