# 3. Literature Review

Version 0.1, 22 October 2026. Part A (scheduling, concurrency, no-shows) — Yerkegali Yergali. Part B (electronic referrals, digital health in Kazakhstan, patient-facing booking UX) — Aigerim Askarova, to be added.

## Search method

| | |
|---|---|
| **Topics (part A)** | Outpatient appointment scheduling and slot selection; patient preferences in scheduling; concurrency control for booking; patient no-shows and their prediction; interventions against no-shows |
| **Where** | PubMed / PubMed Central, Google Scholar, publisher sites (Elsevier, Oxford Academic, MDPI), arXiv, PVLDB |
| **Keywords** | "outpatient appointment scheduling", "patient choice" / "patient preference", "no-show prediction", "missed appointments", "machine learning", "reminder", "serializable isolation" |
| **Period** | Core sources 2021–2025; earlier works kept only if they are foundational reviews or describe a technology the project uses directly |
| **Included** | Peer-reviewed journal articles, conference papers and systematic reviews that inform a concrete requirement or design decision |
| **Excluded** | Blog posts, vendor marketing, patents, papers without accessible abstract or metadata |

## Part A. Structured analysis

| # | Source | Type, year | Focus | Key finding | Limitation | Use in project |
|---|--------|-----------|-------|-------------|------------|----------------|
| A1 | Ala, Chen. *Appointment scheduling problem in complexity systems of the healthcare services: a comprehensive review.* J. Healthcare Engineering | Review, 2022 | Appointment scheduling research 2000–2021 (150+ papers) | Groups the problem by patient flow, patient preferences, and random arrival and service times; patient preferences are an established factor in scheduling models | Mostly models for clinic operations, not citizen-facing booking portals | Justifies including time-of-day convenience (P) in the slot score, F4 |
| A2 | Ahmadi-Javid, Jalali, Klassen. *Outpatient appointment systems in healthcare: a review of optimization studies.* European J. Operational Research | Review, 2017 (foundational) | Optimization of outpatient appointment systems | Organizes decisions into strategic, tactical and operational levels and compares problem settings, environmental factors, models and solution methods | Older; optimization view of the clinic, little on patient-side tools | Frames our algorithm as an operational-level decision: choosing a slot for a given request |
| A3 | Yan, Huang, Kuo, Tang. *Dynamic appointment scheduling for outpatient clinics with multiple physicians and patient choice.* J. Management Science and Engineering | Journal article, 2022 | Online scheduling with several physicians when patients prefer certain doctors and times | Patients may reject an undesirable slot or accept it with a higher no-show probability; good scheduling decisions depend on patient choice | Objective is clinic profit; evaluated on simulated data | Supports recommending several ranked slots (top-5) instead of one, and links slot convenience with no-show risk, F4, F20 |
| A4 | Ports, Grittner. *Serializable Snapshot Isolation in PostgreSQL.* Proc. VLDB Endowment 5(12) | Conference paper, 2012 (foundational) | Serializable transactions in PostgreSQL | Describes PostgreSQL's serializable isolation level; performance only slightly below snapshot isolation and better than two-phase locking on read-heavy workloads | General database work, not healthcare | Background for the transaction level of double-booking protection; we combine row locking with a database constraint, NFR-D |
| A5 | Dantas, Fleck, Cyrino Oliveira, Hamacher. *No-shows in appointment scheduling — a systematic literature review.* Health Policy 122(4) | Systematic review, 2018 (foundational) | No-show rates and determinants, 105 studies | Average no-show rate of the order of 23 % (from 13 % in Oceania to 43 % in Africa); common determinants include long lead time between booking and visit, prior no-shows and distance to the clinic | Pre-2018 studies; rates differ strongly between settings | Quantifies the problem (P3) and gives the first features for the no-show model: lead time, history, distance, F20 |
| A6 | Carreras-García, Delgado-Gómez, Llorente-Fernández, Arribas-Gil. *Patient no-show prediction: a systematic literature review.* Entropy 22(6) | Systematic review, 2020 | Models and predictors used for no-show prediction | Maps which predictors and model families are used in no-show prediction studies | Predates most recent ML work | Feature selection and choice of evaluation metrics, F20 |
| A7 | Salazar, Parreira, Fernandes, Leithardt. *No-show in medical appointments with machine learning techniques: a systematic literature review.* Information 13(11) | Systematic review, 2022 | Machine learning methods for no-show prediction | Reviews ML techniques and the factors most often used for prediction | Results depend on the dataset and setting of each study | Supports comparing logistic regression with tree-based models (Random Forest, XGBoost), F20 |
| A8 | Toffaha et al. *Predicting patient no-shows using machine learning: a comprehensive review and future research agenda.* Intelligence-Based Medicine | Review, 2025 | ML no-show prediction, 52 publications 2010–2025 | Logistic regression is the most common model (68 % of studies); more complex models are gaining ground | Few real deployments reported | Logistic regression as the baseline model; motivates checking whether more complex models add value, F20 |
| A9 | Oikonomidi, Norman, McGarrigle, Stokes, van der Veer, Dowding. *Predictive model-based interventions to reduce outpatient no-shows: a rapid systematic review.* JAMIA 30(3) | Rapid systematic review, 2023 | Interventions that act on predicted no-show risk (7 RCTs, 1 non-RCT) | Model-targeted text reminders reduce no-shows (high certainty); targeted phone reminders and patient navigators probably reduce them (moderate certainty); effect of overbooking uncertain | Calls for comparison of targeted vs non-targeted reminders; little evidence on cost and equity | Design of the extra reminder for high-risk patients and the evaluation baseline "remind everyone", F15, F20 |

Recent sources (2021–2025): A1, A3, A7, A8, A9. Foundational: A2, A4, A5, A6.

## Part A. Summary

**What is known.** Appointment scheduling is a mature research area, mostly from the clinic's perspective, and patient preferences are an accepted part of scheduling models (A1–A3). No-shows are frequent, about a quarter of appointments on average, and are linked to long lead times, previous no-shows and distance (A5). Prediction models are well studied, with logistic regression as the most common baseline (A6–A8), and reminders targeted at high-risk patients are the best-supported intervention (A9).

**Limitations we take into account.** Most scheduling models optimize clinic metrics on simulated data (A1–A3); no-show models depend on the setting where they are trained (A7, A8); and the added value of targeting reminders compared with reminding everyone is still unclear (A9). Therefore the project compares the slot algorithm with a simple "earliest slot" baseline, compares the no-show models with a logistic regression baseline, and evaluates targeted reminders against reminders sent to everyone.

**Gap for part A.** The reviewed works treat slot selection, booking correctness and no-shows separately. None of them describes a patient-facing system that ranks slots across doctors by waiting time, distance and convenience and at the same time uses the patient's no-show risk. This is rechecked after part B is added.

## References (part A)

- [A1] A. Ala, F. Chen, "Appointment scheduling problem in complexity systems of the healthcare services: a comprehensive review," *J. Healthcare Engineering*, vol. 2022, art. 5819813, 2022. doi:10.1155/2022/5819813. https://pmc.ncbi.nlm.nih.gov/articles/PMC8913063/
- [A2] A. Ahmadi-Javid, Z. Jalali, K. J. Klassen, "Outpatient appointment systems in healthcare: a review of optimization studies," *European J. Operational Research*, vol. 258, no. 1, pp. 3–34, 2017. doi:10.1016/j.ejor.2016.06.064
- [A3] C. Yan, G. Q. Huang, Y.-H. Kuo, J. Tang, "Dynamic appointment scheduling for outpatient clinics with multiple physicians and patient choice," *J. Management Science and Engineering*, vol. 7, no. 1, pp. 19–35, 2022. doi:10.1016/j.jmse.2021.02.002
- [A4] D. R. K. Ports, K. Grittner, "Serializable Snapshot Isolation in PostgreSQL," *Proc. VLDB Endowment*, vol. 5, no. 12, pp. 1850–1861, 2012. doi:10.14778/2367502.2367523. https://arxiv.org/abs/1208.4179
- [A5] L. F. Dantas, J. L. Fleck, F. L. Cyrino Oliveira, S. Hamacher, "No-shows in appointment scheduling — a systematic literature review," *Health Policy*, vol. 122, no. 4, pp. 412–421, 2018. doi:10.1016/j.healthpol.2018.02.002
- [A6] D. Carreras-García, D. Delgado-Gómez, F. Llorente-Fernández, A. Arribas-Gil, "Patient no-show prediction: a systematic literature review," *Entropy*, vol. 22, no. 6, art. 675, 2020. https://pmc.ncbi.nlm.nih.gov/articles/PMC7517206/
- [A7] L. H. A. Salazar, W. D. Parreira, A. M. R. Fernandes, V. R. Q. Leithardt, "No-show in medical appointments with machine learning techniques: a systematic literature review," *Information*, vol. 13, no. 11, art. 507, 2022. doi:10.3390/info13110507
- [A8] K. M. Toffaha et al., "Predicting patient no-shows using machine learning: a comprehensive review and future research agenda," *Intelligence-Based Medicine*, art. 100229, 2025. https://khazna.ku.ac.ae/en/publications/predicting-patient-no-shows-using-machine-learning-a-comprehensiv/
- [A9] T. Oikonomidi, G. Norman, L. McGarrigle, J. Stokes, S. N. van der Veer, D. Dowding, "Predictive model-based interventions to reduce outpatient no-shows: a rapid systematic review," *J. Am. Med. Inform. Assoc.*, vol. 30, no. 3, pp. 559–569, 2023. doi:10.1093/jamia/ocac242
