# Venues, reviewer expectations and a Paper C check for the RGB–NIR papers (as of 2026-11-03)

Scope: three deliverables for an MS student planning Paper A (A*-level: physics-informed, counterfactually supervised RGB–NIR fusion gates), Paper B (Q1: "Does NIR make low-light enhancement useful for machines?") and a backup Paper C (camera-conditioned spectral complementarity, PID and conformal).

How this was done: official CFP and author-guideline pages were downloaded and read. LaTeX sources of 7 accepted papers were pulled from arXiv, and their figure/table captions and statistics wording were extracted with a script. OpenReview reviews came from the public search API. No experiments were run.

Labels used below:
- **[EST]** means an estimate.
- **[AGG]** means a third-party aggregator, not the publisher.
- **[UV]** means unverified.

---

## Key question 1 — Venue facts for 2027 (deadlines, page limits, review model, acceptance rates; Q1 journal facts)

### Takeaway
Only CVPR 2027 has published dates beyond what the student can still hit. Its deadline is **Nov 16, 2026 AoE**, 13 days from now, so it is realistic only for a nearly finished paper. Every 2027/28 target below is estimated from its 2026 cycle:
- ICCV 2027: early March 2027.
- ACM MM 2027: about Apr 1, 2027.
- NeurIPS 2027: early–mid May 2027.
- BMVC 2027: late May 2027.
- WACV 2028: Round 1 late June 2027, Round 2 late Aug 2027.
- AAAI-28: late July 2027.
- CVPR 2028: mid-Nov 2027.

Acceptance rates at the A* venues sit at about 18–28%. Among the Q1 journals, TIP and Information Fusion have the highest impact factors. TMM has the tightest page limit (10 pages at first submission). All the IEEE and Elsevier titles allow arXiv preprints.

### Cited Findings

**Conferences**

| Venue | Deadline: official (latest cycle) and estimate for the target cycle | Page limit | Review model and policies | Acceptance rate |
|---|---|---|---|---|
| **ICCV 2027** | ICCV 2027 is Oct 3–7, 2027 at HKCEC, Hong Kong ([ICCV home](https://iccv.thecvf.com/)). The 2027 Dates page lists **no deadlines yet** ([ICCV 2027 Dates](https://iccv.thecvf.com/Conferences/2027/Dates)). ICCV 2025 had paper registration Mar 3, 2025, submission and supplementary material due **Mar 7, 2025 11:59pm HST**, reviews May 9, rebuttal May 10–16 and final decisions Jun 26 ([ICCV 2025 CFP](https://iccv.thecvf.com/Conferences/2025/CallForPapers)). **ICCV 2027 deadline [EST]: early March 2027.** | ICCV 2025: 8 pages including figures and tables, plus reference-only pages ([ICCV 2025 Author Guidelines](https://iccv.thecvf.com/Conferences/2025/AuthorGuidelines)) | OpenReview, double-blind ([ICCV 2025 CFP](https://iccv.thecvf.com/Conferences/2025/CallForPapers)) | ICCV 2025: **24%** (2,698 / 11,239) ([myhuiban](https://www.myhuiban.com/conference/109?lang=en_us); [HRBEU news](https://english.hrbeu.edu.cn/info/1101/4125.htm)). A Paper-Copilot-based count gives 11,152 submissions and 24.2% ([aiwiki](https://www.aiwiki.ai/wiki/iccv)). |
| **CVPR 2028** | CVPR 2027 (official): enrollment Nov 10, 2026; **submission Nov 16, 2026 AoE**; supplementary Nov 23; reviews Jan 25, 2027; rebuttal Jan 25–Feb 1; decisions Feb 25, 2027 ([CVPR 2027 CFP](https://cvpr.thecvf.com/Conferences/2027/CallForPapers)). The Dates page lists the same deadlines in UTC (registration "2026/11/11 11:59:59 UTC", submission "2026/11/17 11:59:59 UTC") ([CVPR 2027 Dates](https://cvpr.thecvf.com/Conferences/2027/Dates)). **CVPR 2028 deadline [EST]: about mid-Nov 2027.** | 8 pages including figures and tables. Extra pages may hold only references and the **mandatory LLM Usage Statement**; papers without it are desk-rejected ([CVPR 2027 Author Guidelines](https://cvpr.thecvf.com/Conferences/2027/AuthorGuidelines)). | OpenReview, double-blind; reviews are never made public. New for 2027 is a **reciprocal-reviewer quota**: if no author on a paper has an accepted main-track paper at CVPR, ICCV, ECCV, WACV, ACM MM, TPAMI, IJCV, NeurIPS, ICML, ICLR, AAAI, IJCAI, JMLR (or the listed robotics and graphics venues), that group may submit at most one paper. Conference-approved LLMs are used in review. Work public on or after **Sept 15, 2026** counts as contemporaneous. arXiv preprints are not "publications" for the dual-submission rule. Max 25 papers per author. ([CVPR 2027 Author Guidelines](https://cvpr.thecvf.com/Conferences/2027/AuthorGuidelines); [CVPR 2027 CFP](https://cvpr.thecvf.com/Conferences/2027/CallForPapers)) | CVPR 2026: 16,092 valid submissions, **25.4%** (about 4,072 accepted) ([CVPR 2026 closing](https://cvpr.thecvf.com/Conferences/2026/News/Closing), via search summary). Third parties report 4,071–4,090 accepted ([besthub](https://www.besthub.dev/articles/cvpr-2026-awards-spotlight-d4rt-resnet-and-the-rise-of-4d-vision-ai-cd9b71177ecb); [Bohrium](https://www.bohrium.com/en/blog/research-notes/cvpr-2026-accepted-papers-highlights/)). CVPR 2025: 22.1% (2,872 / 13,008) ([Conference-Acceptance-Rate list](https://github.com/lixin4ever/Conference-Acceptance-Rate)). |
| **ACM MM 2027** | MM 2026 main track (official): registration Mar 25, **submission Apr 1, 2026**, supplementary Apr 8, rebuttal Jun 4, notification Jul 9, camera-ready Aug 6 ([MM 2026 dates](https://2026.acmmm.org/site/important-dates.html)). MM 2026 is in Rio de Janeiro, Nov 10–14, 2026 ([MM 2026 home](https://2026.acmmm.org/)). **MM 2027 deadline [EST]: about late Mar / early Apr 2027.** myhuiban also estimates "around 2027-04" [AGG] ([myhuiban](https://www.myhuiban.com/conference/53?lang=en_us)). | Up to **8 pages + 2 pages of references**; the CFP says 6–8 pages, and appendices may not follow the main paper in the submission file ([MM 2026 Author Instructions](https://2026.acmmm.org/site/author-instructions.html); [MM 2026 CFP](https://2026.acmmm.org/site/cfp-guidelines.html)) | OpenReview with a rebuttal. **Final reviews and author responses are made public** on OpenReview after the process ends. Authors must review, or face desk rejection. arXiv technical reports are not treated as prior publications. ([MM 2026 CFP](https://2026.acmmm.org/site/cfp-guidelines.html)) | MM 2024: **26.2%** (1,149 / 4,385). MM 2023: 24.6% (902 / 3,669) ([myhuiban](https://www.myhuiban.com/conference/53?lang=en_us)). I found **no MM 2025 rate**. |
| **NeurIPS 2027** | NeurIPS 2026 (official): abstract **May 4, 2026**, full paper **May 6, 2026 AoE**, notification Sept 24, 2026 ([NeurIPS 2026 CFP](https://neurips.cc/Conferences/2026/CallForPapers)). **NeurIPS 2027 [EST]: early–mid May 2027.** | **9 content pages**. References, appendices and the mandatory paper checklist do not count. Camera-ready gets one extra page. ([NeurIPS 2026 Main Track Handbook](https://neurips.cc/Conferences/2026/MainTrackHandbook)) | Double-blind on OpenReview, with reciprocal reviewing. Non-anonymous preprints are allowed, but "aggressive advertising" is not. Work online after Mar 1, 2026 counts as contemporaneous. The checklist covers reproducibility and error bars. ([Handbook](https://neurips.cc/Conferences/2026/MainTrackHandbook)) | 2025: **24.52%** (5,290 / 21,575) ([NeurIPS blog](https://blog.neurips.cc/?p=1377); [myhuiban](https://www.myhuiban.com/conference/3214?lang=en_us)). 2024: 25.8%. **I found no published 2026 main-track rate.** |
| **AAAI-28** | AAAI-27 (official): abstracts **Jul 21, 2026**, full papers **Jul 28, 2026**, supplementary and code Jul 31 (all AoE) ([AAAI-27 main track call](https://aaai.org/conference/aaai/aaai-27/main-technical-track-call/)). AAAI-27 is Feb 16–23, 2027 in Montréal ([AAAI-27](https://aaai.org/conference/aaai/aaai-27/)). **AAAI-28 [EST]: late July 2027.** | **7 pages of content**, 9 pages total with references; reproducibility checklist required ([AAAI-27 call](https://aaai.org/conference/aaai/aaai-27/main-technical-track-call/)) | Two phases. Phase 1 gives two human reviews plus one **AI-generated, non-decisional review**. Papers that survive get more reviews in Phase 2, then an author response. ([AAAI-27 call](https://aaai.org/conference/aaai/aaai-27/main-technical-track-call/)) | AAAI-26: **17.6%** (4,167 / 23,680) ([Bohrium](https://www.bohrium.com/blog/research-notes/aaai-2026-accepted-papers-highlights/); [CSPaper forum](https://forum.cspaper.org/post/407)). The official front matter says nearly 29,000 were submitted and about 23,000 went to review (via search summary of [AAAI OJS](https://ojs.aaai.org/index.php/AAAI/issue/view/732)). AAAI-25: 23.4% (3,032 / 12,957) ([myhuiban](https://www.myhuiban.com/conference/408?lang=en_us)). |
| **WACV 2028** | WACV 2027 (official). Round 1: registration Jun 19, **submission Jun 26, 2026**, decisions Aug 9. Round 2: new-paper registration Aug 21, **Round 1 revisions and Round 2 new papers Aug 28, 2026**, final decisions Oct 14. Camera-ready Nov 2, 2026. ([WACV 2027 Dates](https://wacv.thecvf.com/Conferences/2027/Dates)) The conference is Jan 4–8, 2027 ([WACV home](https://wacv.thecvf.com/)). **WACV 2028 [EST]: Round 1 about late Jun 2027, Round 2 about late Aug 2027.** | 8 pages plus reference pages ([WACV 2027 Author Guides](https://wacv.thecvf.com/Conferences/2027/AuthorGuides)) | Two rounds. Round 1 papers can be invited to "Revise and Resubmit". Three tracks: **Algorithms, Applications, and Evaluations & Datasets**, with different review criteria ([WACV 2027 CFP](https://wacv.thecvf.com/Conferences/2027/CallForPapers)). arXiv is not prior art ([Author Guides](https://wacv.thecvf.com/Conferences/2027/AuthorGuides)). | WACV 2026 Round 1: 1,329 valid submissions; 85 accepted (6.4%), 732 invited to Round 2, 507 rejected. Overall rate about **26.4%** per author pages [UV] ([leejaymin](https://leejaymin.github.io/news/announcement_24); [binhu85](https://binhu85.github.io/news/announcement_15); [UNICT](https://iplab.dmi.unict.it/news/wacv-2026/)). |
| **BMVC 2027** | BMVC 2026 (official): abstract **May 22, 2026**, paper and supplementary **May 29, 2026**, camera-ready Aug 28. The conference is Nov 23–26, 2026 in Lancaster ([BMVC 2026 CFP](https://bmvc2026.bmva.org/calls/call-for-papers/)). **BMVC 2027 [EST]: late May 2027.** | **14 pages excluding references** ([BMVC 2026 Author Guidelines](https://bmvc2026.bmva.org/authors/author-guidelines/)) | Double-blind. arXiv versions are not prior art. Supplementary material may **not** contain results on additional datasets. ([BMVC Author Guidelines](https://bmvc2026.bmva.org/authors/author-guidelines/)) | 2026: 404 / 1,448 (**27.9%**, my arithmetic). 2025: 276 / 865 (31.9%). 2024: 263 / 1,020 (25.8%) ([BMVA statistics](https://www.bmva.org/bmvc/statistics); [NTT](https://group.ntt/en/topics/2025/11/14/bmvc2025.html)). |

**Q1 journals**

| Journal | Impact factor, JCR 2025 release (2024 IF) | Impact factor, JCR 2026 release (2025 IF) [AGG] | Page limit | arXiv / preprints | Review time |
|---|---|---|---|---|---|
| **Information Fusion** (Elsevier) | 15.5 ([myhuiban](https://www.myhuiban.com/journal/312?lang=en_us); [bioxbio](https://www.bioxbio.com/journal/INFORM-FUSION)). JCR Q1 ([journalmetrics, via search](https://www.journalmetrics.org/journal/information-fusion)). | 17.4 ([bioxbio "2026 update"](https://www.bioxbio.com/journal/INFORM-FUSION)) | Not verified: ScienceDirect's Guide for Authors returned HTTP 403 | Elsevier lets authors share a preprint "anywhere at any time", except some double-anonymised journals with their own rules ([Elsevier sharing policy](https://www.elsevier.com/about/policies-and-standards/sharing)) | No official figure found. Aggregators give time-to-publish of 4 months ([Editage](https://www.editage.com/research-solutions/journal/information-fusion/3012)) vs 8 months ([researcher.life](https://researcher.life/journal/information-fusion/3012)), so the numbers conflict. |
| **IEEE TIP** | 13.7 ([IEEE SPS TIP "about" page](https://signalprocessingsociety.org/publications-resources/ieee-transactions-image-processing/about-transactions-image-processing); [myhuiban](https://www.myhuiban.com/journal/140?lang=en_us)). CCF-A. | 15.3 ([bioxbio](https://www.bioxbio.com/journal/IEEE-T-IMAGE-PROCESS)) | **13 double-column pages at first submission, all-inclusive. Revisions ≤16 pages, with appendices moved to supplementary material. Mandatory page charges of $220/page beyond 10 published pages.** ([SPS Information for Authors](https://signalprocessingsociety.org/publications-resources/information-authors)) | Allowed on arXiv. Preprints not identical to the submission count as prior work ([SPS Information for Authors](https://signalprocessingsociety.org/publications-resources/information-authors)) | No official figure found |
| **Pattern Recognition** (Elsevier) | 7.6 ([myhuiban](https://www.myhuiban.com/journal/198?lang=en_us); [bioxbio](https://www.bioxbio.com/journal/PATTERN-RECOGN)). CCF-B. | 9.1 ([bioxbio](https://www.bioxbio.com/journal/PATTERN-RECOGN)) | **20–35 pages, single-column, double-spaced (40 for reviews)**, as quoted by third-party copies of the guide [UV] ([search summary of shop.elsevier.com / myhuiban](https://shop.elsevier.com/journals/pattern-recognition/0031-3203)) | Elsevier policy as above | About 7 months to acceptance per [AGG] ([researcher.life](https://researcher.life/journal/pattern-recognition/3717)) |
| **IEEE TCSVT** | 11.1 ([bioxbio](https://www.bioxbio.com/journal/IEEE-T-CIRC-SYST-VID)). The IEEE CAS page still shows **8.4** and CiteScore 13.8 with no year, which looks stale ([IEEE CAS TCSVT](https://ieee-cas.org/publication/tcsvt)). | 10.8 ([bioxbio](https://www.bioxbio.com/journal/IEEE-T-CIRC-SYST-VID)) | **At most 14 two-column pages including figures. Overlength charges start at page 12.** The same page also mentions a $175/page charge "in excess of nine" when the voluntary charge is not paid, which is inconsistent. ([IEEE CAS TCSVT submission page](https://ieee-cas.org/node/3113)) | General IEEE policy allows arXiv [UV for CAS specifically] | No official figure found |
| **IEEE TMM** | 9.7 ([Wikipedia/JCR](https://en.wikipedia.org/wiki/IEEE_Transactions_on_Multimedia); [bioxbio](https://www.bioxbio.com/journal/IEEE-T-MULTIMEDIA)) | 9.9 ([bioxbio](https://www.bioxbio.com/journal/IEEE-T-MULTIMEDIA)) | **10 pages at first submission, 14 at revision. Mandatory page charges beyond 8 pages.** ([SPS Information for Authors](https://signalprocessingsociety.org/publications-resources/information-authors)) | Allowed (SPS policy, as for TIP) | No official figure found |
| **IEEE TCI** | 4.7–4.8, Q1 ([myhuiban](https://www.myhuiban.com/journal/1144?lang=en_us); [wos-journal.info](https://wos-journal.info/journalid/13543); [journalmetrics, via search](https://www.journalmetrics.org/journal/ieee-transactions-on-computational-imaging)). SciJournal says 5.36 [AGG, conflicting] ([scijournal](https://scijournal.org/ieee-transactions-on-computational-imaging)). | Not found | Same SPS rule as TIP: **13 pages initial, 16 revised** ([SPS Information for Authors](https://signalprocessingsociety.org/publications-resources/information-authors)) | Allowed (SPS policy) | No official figure found |
| **IEEE TITS** | 8.4 ([bioxbio](https://www.bioxbio.com/journal/IEEE-T-INTELL-TRANSP)) | **9.1 (2025)**, shown on the official ITSS page ([IEEE ITSS T-ITS](https://ieee-itss.org/pub/t-its/)) | **Suggested 10 pages; at most 6 extra pages at $175 each. At most 3 review rounds, and no major revision allowed after round 2.** ([IEEE ITSS T-ITS](https://ieee-itss.org/pub/t-its/)) | General IEEE policy [UV] | Not stated on the page |

- **Cross-check on the IF columns.** The bioxbio "2025 (2026 update)" figure for TITS (9.1) matches the official ITSS page. That makes the bioxbio 2025 column plausible, though still [AGG]. Its 2024 column matches myhuiban, Wikipedia/JCR and IEEE SPS for TIP (13.7), TMM (9.7), Information Fusion (15.5) and PR (7.6). ([bioxbio TITS](https://www.bioxbio.com/journal/IEEE-T-INTELL-TRANSP); [ITSS](https://ieee-itss.org/pub/t-its/))

### Inferences
- **Calendar for one MS student.** Paper A's realistic A* targets are ICCV 2027 (~early Mar 2027) and, as fallbacks, ACM MM 2027 (~Apr 1) or NeurIPS 2027 (~May). After that come WACV 2028 Round 1 (~late Jun 2027), AAAI-28 (~late Jul), WACV Round 2 (~late Aug) and CVPR 2028 (~mid-Nov 2027). CVPR 2027 (Nov 16, 2026) is too close for a new paper.
- **Venue fit for Paper B.** WACV's **Evaluations & Datasets track** explicitly asks for papers that "compare evaluation designs and demonstrate how different assumptions lead to different results or conclusions" ([WACV 2027 CFP](https://wacv.thecvf.com/Conferences/2027/CallForPapers)). That is close to Paper B's protocol study, so WACV E&D is a credible conference alternative to a Q1 journal.
- **Venue fit for Papers A and C.** ACM MM publishes its reviews. A rejection there leaves a public record that the next venue's reviewers can find; this is a strategic cost.
- **The CVPR reciprocal-reviewer rule.** An MS student with no eligible publication can submit only one paper to CVPR 2027/2028 unless a co-author is eligible. HAQAGen (WACV 2026) should make the group eligible, since WACV is on the list ([arXiv 2601.01103](https://arxiv.org/abs/2601.01103); [CVPR 2027 AG](https://cvpr.thecvf.com/Conferences/2027/AuthorGuidelines)).
- **Journal choice for Paper B.** TMM's 10-page first-submission limit is tight for a protocol study with 5 protocols and many tables. TIP/TCI (13 pages), TCSVT (14) and PR (35 single-column pages) leave more room. For a fusion-method paper, Information Fusion has the highest IF in the set.
- **An empirical review-time anchor.** NAID first appeared on arXiv on 2024-04-12 and carries "Accepted by IEEE TMM" on v4, whose source is dated March 2025 ([arXiv 2404.08514](https://arxiv.org/abs/2404.08514)). That suggests submission to acceptance of roughly 11 months or less; this is an inference from arXiv timestamps, not an official figure.

### Gaps
- No official ICCV 2027, ACM MM 2027, NeurIPS 2027, AAAI-28, WACV 2028 or BMVC 2027 deadlines are published yet; all are [EST].
- No official NeurIPS 2026 or ACM MM 2025 acceptance rate was found. The WACV 2026 overall rate is from authors' pages only.
- Official guides for Information Fusion and Pattern Recognition could not be fetched (ScienceDirect returned 403), so their page limits and review models are [UV]. Whether either journal uses double-anonymised review, which can change the preprint policy, is unknown.
- I found no publisher-reported "submission to first decision" times for any of the seven journals. The aggregator figures conflict.
- Clarivate JCR was not directly accessible. The 2025-IF (JCR 2026) values are aggregator values, except TITS.

---

## Key question 2 — What reviewers expect: datasets, baselines, ablations, statistics and the figure set in accepted 2024–2026 RGB-X / RGB-NIR papers, plus public rejection reasons

### Takeaway
The accepted papers share a common pattern:
- 2–7 datasets, which drops to 2–3 when one of them is new or the protocol is unusual.
- 7–38 baselines.
- 1–9 ablation tables.
- A fixed figure set: a "paradigm comparison" teaser, an architecture overview plus module close-ups, qualitative grids, and at least one interpretability figure such as gate weights, attention or t-SNE.

Almost none report seeds or error bars. FuseAdapt (NeurIPS'26) explicitly says its comparisons are single runs.

The public OpenReview reviews show where rejections come from:
- "dual-encoder + cross-attention" incremental novelty
- marginal gains outside one dataset
- no wall-clock or memory numbers
- gates or "reliability" that are not shown to differ from attention
- counterfactual or reliability heuristics with unquantified cost
- for evaluation-protocol papers, the fairness of the protocol itself (augmentations, detector choice).

### Cited Findings

**Per-paper audit.** Counts come from the arXiv LaTeX sources. "Main" is the material before the appendix.

| Paper (venue) | Datasets | Baselines in main SOTA tables | Ablation / analysis tables | Seeds / statistics | Figures (main paper) |
|---|---|---|---|---|---|
| **StitchFusion** (ACM MM'25 per earlier note; the arXiv comment gives no venue [UV]) ([arXiv 2408.01343](https://arxiv.org/abs/2408.01343)) | **7**: NYUDv2, SUN-RGBD, DeLiVER, MCubeS, FMB, PST900, MFNet. An 8th, the new MMUS set, is in the appendix. | About **30** unique cited methods across T1–T2 | Main: 4 ablations (stage position, adapter density, hidden dim, modality combinations) plus 2 efficiency tables (parameters; GFLOPs). Appendix: 4 per-class tables and 1 parameter table. | None reported | **5**: F1 fusion-paradigm comparison teaser; F2 framework; F3 detailed architecture and fusion module; F4 variants for different encoders; F5 qualitative MCubeS. Appendix (8): modality illustration, adapter densities, **per-class IoU bars**, **t-SNE ×2**, extra qualitative ×3. |
| **FuseAdapt** (NeurIPS'26; "25 pages, 6 figures, 19 tables") ([arXiv 2610.21779](https://arxiv.org/abs/2610.21779)) | **3** (MCubeS, MUSES, DELIVER) × 2 settings (full-modality MCSS; missing-modality MISS) | About **16** unique | Main: 6 SOTA tables plus 2 analyses (information-flow variants; auxiliary objective). Appendix: 11 more, covering rank, LoRA insertion interval, routing, choice of VFM, objective controls, shift robustness, MiT-B0/B2 controls and an **H100 latency / throughput / memory table**. | States that "segmentation comparisons are reported from **single runs**, without a statistical-significance claim". Uses seed 3407; one analysis reports "mean drop ± SD … across the **three seeds**". | **4**: F1 fusion-strategy comparison; F2 comparison of missing-modality designs; F3 overview; F4 qualitative MISS grid vs CMNeXt and StitchFusion. Appendix: 2 **LoRA cosine-similarity heatmaps** (interpretability). |
| **DFormerv2** (CVPR'25) ([arXiv 2504.04701](https://arxiv.org/abs/2504.04701)) | **3** (NYUDv2, SUN-RGBD, DeLiVER) | About **18** | 3 ablations (roadmap from vanilla attention to GSA; prior-bridging operations; decay strategies), 1 **latency** table, 1 modality-effect analysis | None | **9**: F1 pipeline comparison teaser; F2 attention-mechanism comparison; F3 geometry-prior illustration; F4 architecture; F5 **performance-vs-computation bubble plot**; F6 qualitative grid; F7 prior visualization; F8 **attention visualization**; F9 features with and without the prior. |
| **Sigma** (WACV'25) ([arXiv 2404.04256](https://arxiv.org/abs/2404.04256)) | **4** (MFNet, PST900, NYUDv2, SUN RGB-D) | About **38** | Main: 2 (components; attention matrix). Appendix: day/night split, decoder and state-size ablation, complexity table. | None | **8**: F1 **FLOPs/parameters bubble chart** as teaser; F2 overall architecture; F3–F5 block diagrams; F6–F7 qualitative grids; F8 single- vs multi-modal comparison. Appendix: complexity plots and extra qualitative results. Has a Limitations section. |
| **CAFuser** (RA-L'25) ([arXiv 2410.10791](https://arxiv.org/abs/2410.10791)) | **2** (MUSES panoptic + semantic; DeLiVER) | About **9** | **6** (module ablation; weighting strategies; CA² design; contrastive guidance; prompt encodings; per-modality addition) | None | **6**: F1 condition-token teaser; F2 architecture; F3–F4 module diagrams; **F5 average fusion weights per sensor by weather and time of day (a gate visualization)**; F6 qualitative grid showing all four input modalities. |
| **TDFusion** (CVPR'25) ([arXiv 2412.03240](https://arxiv.org/abs/2412.03240)) | **4** (MSRS, FMB, M3FD, LLVIP), with segmentation **and** detection downstream | **7** fusion methods (TarDAL, SegMIF, MURF, EMMA, DCINN, MRFS, TIMFusion) plus IR-only and VIS-only | 1 fusion ablation | None | **5**: F1 workflow; F2 fusion visual comparison; F3 segmentation visual; F4 detection visual; F5 visualization of the learned loss. On LLVIP, the **IR-only** input (AP50 96.03) beats TDFusion (95.00) and every other fusion method in its own Table 2. |
| **NAID / SFM** (IEEE TMM'25) ([arXiv 2404.08514](https://arxiv.org/abs/2404.08514)) | **2** (synthetic DVD; new real dataset Real-NAID) | **9** (Uformer, Restormer, NAFNet, Xformer; FGDNet, CUNet, SANet, MNNet, DVN), plus the module plugged into 4 backbones | **9** (fusion manners; modulation modules; kernel size; GMM/LMM order; doubled modules; block counts; number of SFMs; losses; per-backbone) | None | **12**: discrepancy-example teaser; dataset-construction figure; pipeline comparison; module structure; qualitative DVD; 4 per-backbone single-vs-NIR-assisted figures; qualitative Real-NAID; GMM/LMM visualization; kernel-size visual. Several figures and tables are wrapped in a `\changes{}` macro (67 uses), which suggests they were **added during TMM revision** (inference). |

**Public OpenReview reviews (rejection patterns)**

- **LIME-Eval**, ICLR 2025, Submission 1464. It is a low-light-enhancement evaluation paper built on object detection, and it was **withdrawn**. Ratings: 8 / 8 / 6 / 3 ([OpenReview forum hYU0P4Wlj9](https://openreview.net/forum?id=hYU0P4Wlj9)).
  - Reviewers liked:
    - the finding that "higher detection accuracy does not necessarily correlate with superior enhancement quality"
    - the question "is fine-tuning detectors a valid approach to evaluating enhancers?"
  - Criticisms:
    - Random augmentation makes the input to the detector unfair, so the qualitative comparisons are "inadequate and even wrong" (rating 3).
    - Whether color jitter or normalization interferes with enhancement.
    - Only ExDark and synthetic data were used; reviewers asked for DARK FACE.
    - Missing related work on low-light detection without enhancement: MAET (ICCV'21), Similarity min-max (ICCV'23), DAI-Net (CVPR'24).
    - No analysis of *why* the metric works.
    - Spearman correlation reported against only one IQA method.
    - Requests for more detectors (YOLOX) and recall metrics (AR).
- **"Multimodal Fusion of RGB and Complementary Modalities for Semantic Segmentation"**, ICLR 2026, **withdrawn**. It used 8 datasets and 5 RGB+X pairings and reported 52.54 mIoU on MCubeS. Ratings: 2 / 4 / 4 / 4 ([OpenReview wBD61QFEpm](https://openreview.net/forum?id=wBD61QFEpm)). Criticisms:
  - "still a 'dual encoder + cross-attention + consistency' network, like many RGB-X papers in 2022–2025"
  - gains marginal outside MFNet, raising possible over-tuning
  - "no wall-clock runtime or memory usage analysis"
  - no visualization of what the patch selector picks, and an unaddressed risk of one modality dominating
  - a coarse patch grid that is "not aligned with semantic content"
  - a lower-resolution comparison on DELIVER
  - requests to compare against a list of existing fusion modules and add t-SNE / feature visualizations
  - missing baselines: OmniSegmentor, MemorySAM, AnySeg.
- **"Reliability-Guided Gradient Correction for Visible-Infrared Object Detection"**, ICLR 2026, **withdrawn**. All four reviews rated it **4** ([OpenReview i2JDV6F88U](https://openreview.net/forum?id=i2JDV6F88U)). Criticisms:
  - "reliability" is vague and "reads like an attention-style operation"
  - "counterfactual reasoning for effectiveness" looks heuristic
  - the cost of the counterfactual computation is not quantified
  - the reliability measure may be sensitive to batch size, learning rate and optimizer
  - "straightforward combination" of uncertainty and gradient calibration
  - "rather marginal" gains, outdated baselines, and no component ablation
  - the primary benchmark sits in the appendix and shows weaker gains.
- **ICLR 2026 Submission 2630**, an information-theoretic fusion paper rated 4. A reviewer asked for **empirical MI estimates** to verify claimed MI reduction, saying theoretical bounds that rest on Gaussian assumptions are not enough ([OpenReview search result, forum Jz5OcxVJbP](https://openreview.net/forum?id=Jz5OcxVJbP)).
- **NeurIPS 2024 Submission 4309**, rated 6. A reviewer asked whether the method extends to "the typical fusion of near-infrared and visible bands" ([OpenReview yBrxziByeG](https://openreview.net/forum?id=yBrxziByeG)).
- **NeurIPS 2024 Submission 35**, rated 5. Reviewers said the principle behind the gate definition was not explained, and the experiments did not show the claimed advantage in dynamic scenes ([OpenReview RSs4o7CSqe](https://openreview.net/forum?id=RSs4o7CSqe)).

### Inferences
- **Paper A (A*): minimum evidence package.**
  - **Datasets.** At least 3: IDD-AW (core), MCubeS RGB+N, and one RGB-T set such as MFNet or FMB. This matches FuseAdapt and DFormerv2 (3) and sits below StitchFusion and Sigma (4–7).
  - **Baselines.** At least 10–15 recent ones, including CMX, CMNeXt, StitchFusion, DFormer(v2), Sigma, GeminiFusion, FuseAdapt, AnySeg/OmniSegmentor/MemorySAM and CAFuser-style condition gating, all at **the same resolution**.
  - **Ablations.** At least 4 tables:
    - gate source (physics prior vs learned vs counterfactual target)
    - cross-fitting folds K
    - physics terms (γ / τ / noise)
    - per-condition splits.
  - **Efficiency.** A wall-clock latency/memory table and a FLOPs/parameters bubble plot.
  - **Statistics.** 3 seeds with mean ± std on IDD-AW. This is cheap insurance: the norm is single runs, but A's gains will probably be about 1 mIoU, which is within seed noise.
- **Paper A figure set.**
  - F1 teaser: the fusion paradigm, or "learned gate collapses vs physics/counterfactual gate".
  - F2 architecture.
  - F3 gate module.
  - F4 qualitative grid (RGB | NIR | gate map | prediction | GT) across fog, rain, snow and low light.
  - F5 gate weights by condition, like CAFuser's F5, **plus a scatter of gate value vs cross-fitted counterfactual utility**. This answers the "reliability = attention?" critique directly.
  - F6 efficiency bubble plot.
  - Appendix: per-class IoU bars, t-SNE, and failure cases. None of the seven papers has a dedicated failure-case figure, so including one sets the paper apart and pre-empts reviewer doubts.
- **Paper A text.** It must state the cost of the counterfactual computation (it is training-time only, and inference is unchanged). It must also include a hyperparameter-sensitivity study. These are the exact points used against the ICLR 2026 reliability paper.
- **Paper B: protocol fairness is the attack surface.** The LIME-Eval reviews show that:
  - augmentation and normalization choices, the detector family and recall metrics must be fixed and justified;
  - at least 2 downstream architectures and at least 2 real datasets are needed;
  - "no-enhancement" low-light detection and segmentation baselines (domain adaptation and self-supervised methods such as MAET, DAI-Net and similarity min-max) must be cited and ideally included.

  TDFusion's own table, where IR-only beats fusion on LLVIP, shows that reviewers accept single-modality baselines winning. Paper B should report raw-RGB, raw-NIR and stacked-input baselines for every protocol.
- **Paper C: information-theoretic claims need empirical estimator validation.** This means reporting the PID estimates with uncertainty, checking them on synthetic ground truth, and so on, per the ICLR 2026 MI critique.

### Gaps
- StitchFusion's venue (ACM MM'25) could not be confirmed from arXiv metadata; it comes from the earlier wave-1 note.
- I did not read the camera-ready supplementary PDFs, only the arXiv sources, so the appendix counts may differ from the published versions.
- The OpenReview `notes?forum=` endpoint required a browser challenge. Reviews were pulled from the search endpoint, so a few reviews or meta-reviews per forum may be missing. I found no public meta-review for any of the three withdrawn papers.
- I found no public OpenReview reviews of an **accepted** RGB-NIR segmentation paper. CVPR, ICCV and WACV keep reviews private.

---

## Key question 3 — Paper C prior art, novelty score, and whether C is a stronger Q1 than B

### Takeaway
No paper found combines all four parts of Paper C:
- camera-conditioned spectral PID per semantic class,
- its use as a fusion prior,
- PID-defined Mondrian / hierarchical conformal strata,
- a falsifiable rank-correlation with measured per-class fusion gain.

The pieces exist separately, though: Galway's JMIM/CSNR band selection, Liang's PID, a 2026 PID-for-MRI-input-selection paper, and class-conditional or hierarchical conformal segmentation.

**Novelty: about 6.5/10 for a Q1 journal and about 4/10 for A*.** C is more novel than B but riskier. Its central claim can fail on about 19–26 classes, and its fusion-prior half overlaps Paper A.

**Verdict:** C is *not* automatically a stronger Q1 than B. It becomes stronger only if a cheap 2–3-week pilot shows a significant rank correlation. Keep B as the primary Q1 and C as the higher-ceiling backup, aimed at TCI/TIP/PR.

### Cited Findings

**Closest works**
- **Li, Shah et al., "CSNR and JMIM Based Spectral Band Selection for Reducing Metamerism in Urban Driving"** ([arXiv 2508.10962](https://arxiv.org/abs/2508.10962); IEEE OJITS vol. 7, pp. 1021–1033, 2026).
  - The first author is **Jiarong Li**; Imad Ali Shah is second, so "Shah et al." is a misattribution.
  - Using H-City V2, it combines joint mutual information maximization and correlation analysis with a contrast-SNR image-quality metric. It selects three bands, **497, 607 and 895 nm (±27 nm)**, and motivates them by metamerism and vulnerable-road-user safety.
- **Li, Shah et al., "Band-Selection Stability and Semantic Segmentation Performance: A Study on Hyperspectral City"** (WHISPERS 2026; [arXiv 2609.31074](https://arxiv.org/abs/2609.31074)).
  - Six band-selection methods on ten class-balanced ROI sets, using H-City V2 (128 bands, 450–950 nm).
  - Top-K bands with three segmentation models; Sim-LP is the most stable, and Sim-LP and JMIM+CSNR give the best segmentation.
- **The same Galway group is active:** "HSI-Road Relabeled: Surface-Aware Road-Scene Segmentation" (Shah et al., Sept 2026) ([arXiv 2609.12151](https://arxiv.org/abs/2609.12151)).
- **Liang et al., PID** (NeurIPS 2023; [arXiv 2302.12247](https://arxiv.org/abs/2302.12247)): scalable estimators of redundancy, uniqueness and synergy between modalities with respect to a task. It is used to select multimodal models; it is generic, not physics-based or per-class.
- **Chopra & Kurt, "Partial Information Decomposition as a Multi-Contrast 3D MRI Selection Strategy…"** ([arXiv 2607.15396](https://arxiv.org/abs/2607.15396), Jul 2026). This is the **closest methodological analogue** to C's "PID predicts fusion benefit":
  - A PID computed before training ranked input pairs and selected T1c+T2-FLAIR.
  - Of eleven trained U-Nets, that pair was the best two-input model (Dice 0.676 vs 0.687 for all four inputs).
  - The choice was confirmed with Shapley analysis.
  - It works at the modality level, in medical imaging, and is not camera-physics-based.
- **Conformal segmentation:**
  - Mossina et al.: image-level, post-hoc conformal sets for segmentation (CVPRW 2024; [arXiv 2405.05145](https://arxiv.org/abs/2405.05145)).
  - Badjie & Cecílio: pixel-wise **class-conditional** split-conformal for segmentation in safety-critical navigation (Ada-Europe AEiC 2026), with no hierarchy ([Dagstuhl OASIcs](https://drops.dagstuhl.de/entities/document/10.4230/OASIcs.AEiC.2026.1)).
  - ConformalSAM (ICCV 2025; [arXiv 2507.15803](https://arxiv.org/abs/2507.15803)): **class-conditioned** CP used to filter SEEM pseudo-labels in semi-supervised segmentation; a different goal.
- **Hierarchy:**
  - Adaptive Hierarchical Certification (ICML 2024; [arXiv 2402.08400](https://arxiv.org/abs/2402.08400)) relaxes unstable pixels to coarser levels of a hierarchy, under randomized smoothing rather than conformal prediction.
  - Mortier et al. extend split conformal to hierarchical classification with internal-node prediction sets ([arXiv 2501.19038](https://arxiv.org/abs/2501.19038)); classification only.
  - Ding et al. formalise Mondrian / clustered class-conditional conformal prediction, "splitting the data according to a pre-specified taxonomy" (NeurIPS 2023; [arXiv 2306.09335](https://arxiv.org/abs/2306.09335)).
- **Romero et al.** ([arXiv 2511.18473](https://arxiv.org/abs/2511.18473)): metamer-aware uncertainty for **HSI reconstruction**. It uses Bayesian diffusion posteriors and metameric-black augmentation. The task is different (reconstruction, not segmentation or fusion), but it is the closest "metamerism + UQ" paper.
- **The GitHub capstone `krnRaj03/iddaw-safe-segmentation`** is a Walsh College QM640 Data Analytics capstone (Fall 2026) and is at "**synopsis stage**".
  - Its questions are the mIoU vs SmIoU gap, whether NIR helps, a tree-distance loss, and dangerous errors.
  - It uses the ICPR'24 split (3,430 / 475 / 1,095).
  - It has no conformal or PID component and no results ([README](https://raw.githubusercontent.com/krnRaj03/iddaw-safe-segmentation/HEAD/README.md)).
- **arXiv searches** (export.arxiv.org API, 2026-11-03) returned:
  - no paper for conformal + segmentation + hierarchical;
  - only medical papers for conformal + segmentation + multimodal / thermal / infrared;
  - only 2 hits for metamerism + NIR (2508.10962 and an unrelated imaging paper);
  - no hits for hyperspectral + simulation + NIR + segmentation.
- **Wave-1 note scores, for reference:** S2 (complementarity prior) scored Q1 6.5 / A* 4; S5 (ambiguity-aware conformal) scored Q1 6 / A* 3.5 ([wave1_spectral_physics_imaging.md](wave1_spectral_physics_imaging.md)).
- **Data facts carried from wave 1:**
  - ICVL RAW covers 519 bands from 400–1000 nm, but is uncalibrated camera counts under CC BY-NC-ND.
  - H-City V2 covers 450–950 nm with 19 Cityscapes classes, but was H.264-compressed (lossy).
  - IDD-AW's JAI FS-3200D gives VIS 400–670 / NIR 740–1000 nm.
  - ([wave1_datasets_audit.md](wave1_datasets_audit.md))

### Inferences
- **Novelty: Q1 6.5/10, A* 4/10.**
  - What is new: physics-conditioned, per-class PID through the actual IDD-AW camera curves and noise, plus one quantity used both as a training prior and as conformal strata, plus a pre-registered falsification test.
  - Why it is not higher:
    - Each component has a close precedent (JMIM/CSNR band selection; PID-for-modality-selection; class-conditional / Mondrian CP; hierarchical CP).
    - A* reviewers will likely see "two loosely coupled halves" (prior + CP), echoing the "straightforward combination" critique in the ICLR 2026 reliability reviews.
- **Main technical risks.**
  1. **Few classes for the test.** The rank-correlation test has only about 19 (H-City / Cityscapes) to 26 (IDD-AW level-3) classes. The standard two-sided α=0.05 critical Spearman ρ is about 0.45 for n=20 and about 0.39 for n=26 (textbook values; this is background knowledge, not from a fetched source). A moderate true effect could easily fail.
  2. **Material spectra don't map cleanly to classes.** Library spectra (USGS/ECOSTRESS) map poorly to object classes such as "vehicle", which mixes paint, glass and rubber. Using H-City V2 pixels per class avoids this, but adds lossy compression and misses 950–1000 nm.
  3. **Per-class fusion gains are noisy.** On IDD-AW, measuring them needs multiple seeds, and the norm in the field is single runs (FuseAdapt).
  4. **Exchangeability.** Conformal coverage under weather shift breaks exchangeability, so it must be calibrated per condition or with Mondrian strata by weather.
- **Overlap with Paper A.** C's use (i), "class-level prior/regularizer for RGB-NIR fusion" on IDD-AW and MCubeS, is close to A's "physics-informed gates" on the same datasets. Keep them separate:
  - A: per-pixel, condition-dependent (weather/illumination physics + counterfactual targets).
  - C: per-class, material/metamerism-dependent, and centred on **uncertainty** (Safe-mIoU-aware conformal sets).
  - Otherwise reviewers may see salami-slicing.
- **Feasibility on one 24 GB GPU.**
  - The PID and spectral simulation are CPU or light-GPU work.
  - Conformal calibration is post-hoc and cheap.
  - The heavy part is training an RGB-only and an RGB+NIR segmenter (for example SegFormer-B2) on IDD-AW with 3 seeds each. This probably fits 24 GB at 1024×768 with a small batch, though memory was not checked.
  - So C is **cheaper in GPU-hours than B**, which retrains several enhancers and downstream models across 5 protocols. C is heavier in data engineering: camera curves, noise models, the class mapping, and ICVL's 335–405 GB download.
- **Likelihood of measurable gains.**
  - B's main deliverable is a finding that is publishable whichever way it comes out, plus a no-regret enhancer whose guarantee is easy to show.
  - C's headline result (ρ) may come out null. Its mIoU gains from a class prior will probably be under 1 mIoU, as wave 1 predicted.
  - Its conformal results (smaller sets or better Safe-mIoU coverage for NIR-distinct classes) are likely measurable, but must beat a plain class-conditional / Mondrian CP baseline (Ding et al.) to show the physics matters.
- **Recommendation: keep B as the primary Q1.** Run a **go/no-go pilot for C first**:
  - (a) Compute per-class unique-NIR information from H-City V2 pixels rendered through the JAI FS-3200D curves.
  - (b) Measure per-class ΔIoU (RGB+NIR minus RGB) on IDD-AW val with 3 seeds of a small segmenter.
  - (c) If Spearman ρ ≥ about 0.5 with p < 0.05, C becomes a stronger and more distinctive Q1 than B (best fit: TCI or TIP, possibly PR). If not, the null result can become a section of B or A rather than its own paper.
  - The Galway group publishes in this exact space (OJITS 2026, WHISPERS 2026, HSI-Road Sept 2026), so scoop risk for the "NIR breaks metamerism on H-City" framing is real.

### Gaps
- The JAI FS-3200D spectral response curves were not retrieved; whether JAI publishes them numerically is [UV].
- I could not check Semantic Scholar for non-arXiv prior art (HTTP 429 rate limit), so Paper C's prior-art sweep covers arXiv, OpenReview and web search only. A workshop or regional-journal paper on "NIR per-class benefit" could have been missed.
- I did not verify whether the IDD-AW paper or StitchFusion's per-class tables give per-class RGB vs RGB+NIR IoUs that could feed a zero-cost pilot. StitchFusion's appendix has per-class MCubeS IoU, but only for the fused model, as far as the caption shows.
- Whether H-City V2's lossy compression significantly distorts per-class spectra is unknown.

---

## Key question 4 — Are Papers A, B, C distinct from NIR→RGB colorization, and what else has the IISER Bhopal group published on NIR?

### Takeaway
All three papers are distinct from NIR→RGB colorization:
- Paper A uses NIR as an extra input for segmentation.
- Paper B uses NIR as a *guide* for restoring a captured dark RGB image and judges it by downstream tasks.
- Paper C quantifies how much information NIR adds and uses it for fusion and conformal uncertainty.

None of them generates RGB from NIR alone. On arXiv, the group's only NIR paper is **HAQAGen (WACV 2026)**, so there is no direct overlap. One related point: co-author Samiran Das has a hyperspectral-unmixing background, which is useful in-house expertise for Paper C.

### Cited Findings
- **HAQAGen** — "Histogram Assisted Quality Aware Generative Model for Resolution Invariant NIR Image Colorization", by Abhinav Attri, Rajeev Ranjan Dwivedi, Samiran Das and Vinod Kumar Kurmi.
  - Venue: **"Accepted at WACV 2026"** (not just an arXiv preprint).
  - Method: a NIR-to-RGB colorization model using differentiable histogram matching, SPADE hue–saturation priors, a Mamba backbone and adaptive-resolution inference.
  - Sources: [arXiv 2601.01103](https://arxiv.org/abs/2601.01103); [project page](https://rajeev-dw9.github.io/HAQAGen).
- **arXiv author search** (export.arxiv.org, 2026-11-03):
  - "au:Samiran Das" returns 8 papers; the only NIR one is HAQAGen. The others are hyperspectral unmixing (2018, [arXiv 1806.00864](https://arxiv.org/abs/1806.00864)), remote-sensing change detection, meme classification, fruit recognition and mathematics.
  - Vinod K. Kurmi's 2024–2026 arXiv papers cover:
    - domain adaptation (e.g., 3D-BBDA, WACV 2027, [arXiv 2611.03588](https://arxiv.org/abs/2611.03588))
    - uncertainty/energy-based semi-supervised segmentation (WACV 2025, [arXiv 2501.01640](https://arxiv.org/abs/2501.01640))
    - few-shot incremental learning, unlearning (BMVC 2026, [arXiv 2609.17790](https://arxiv.org/abs/2609.17790))
    - fairness, remote-sensing foundation models, medical segmentation.
  - None of Kurmi's papers is on NIR besides HAQAGen.
- A web search for Kurmi / IISER Bhopal + NIR found **no other 2025–2026 NIR paper** from the group ([search summary incl. project page](https://rajeev-dw9.github.io/HAQAGen)).
- **Data overlap with the colorization work.** The group's colorization data (FANVID, VCIP2020/EPFL) has no low-light ground truth or segmentation ground truth. Papers A, B and C instead use IDD-AW, MCubeS, Real-NAID / DVD and spectral libraries. HAQAGen also mis-describes FANVID's band as "700–800 nm, faces & urban" ([wave1_datasets_audit.md](wave1_datasets_audit.md)).
- **The nearest boundary case for Paper B** is Niu et al. (ACM MM 2026, [arXiv 2607.29684](https://arxiv.org/abs/2607.29684)). It needs **no clean RGB** and maps NIR to RGB with Gumbel color codes, so it is effectively colorization-flavoured enhancement ([wave1_nir_assisted_restoration.md](wave1_nir_assisted_restoration.md)).

### Inferences
- **Paper B is the one that sits closest to colorization.**
  - Keep it defined as *NIR-guided enhancement of an observed low-light RGB image*, where the RGB input is always present and the identity/RGB-only fallback gives the "no-regret" guarantee.
  - Evaluate it **only** through downstream tasks and the 5 protocols.
  - Exclude the extreme-darkness regime where RGB carries no signal. That is where methods turn into colorization (Niu MM'26) and where the group's HAQAGen line would overlap.
- **Using HAQAGen as a baseline.** Optionally include HAQAGen-style colorization in Paper B as a *control*: "NIR-only → RGB, then segment". This turns the group's prior work into a baseline rather than an overlap, and it shows explicitly whether hallucinated colour helps machines.
- **Expertise.** Kurmi's uncertainty and domain-adaptation background suits Paper C's conformal half and Paper A's counterfactual/gating half. Das's hyperspectral-unmixing background suits C's spectral simulation.

### Gaps
- Non-arXiv publications by the group (Indian conferences, journals, theses) could not be enumerated because Semantic Scholar rate-limited the requests. A follow-up with DBLP or Google Scholar for Kurmi, Das and Dwivedi is advisable before submission.
- I did not check whether any group member has an unpublished or under-review NIR paper, since internal knowledge is needed.
