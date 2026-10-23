# 3. Literature Review

Version 1.0, 22 October 2026.
Sources on scheduling, concurrency and no-shows — Yerkegali Yergali; sources on referrals, digital health in Kazakhstan, framework benchmarks and official sources — Aigerim Askarova; synthesis — both.

**In total: 21 sources, of them 14 scientific publications (9 from 2021–2025, 5 foundational) and 7 official, news and industry sources.**

---

## 3.1 Search method

| | |
|---|---|
| **Topics** | (1) outpatient appointment scheduling and slot selection; (2) patient no-shows, their prediction and interventions; (3) concurrency control for booking; (4) electronic referrals and waiting times for specialists; (5) health information systems in Kazakhstan; (6) performance of backend frameworks |
| **Where** | PubMed / PubMed Central, Google Scholar, DOAJ, publisher sites (Elsevier, Oxford Academic, MDPI, Springer, Dove Press), arXiv, PVLDB, MATEC; for the Kazakhstan context — official portals and national media |
| **Keywords** | "outpatient appointment scheduling", "patient choice", "no-show prediction", "missed appointments", "reminder", "machine learning", "serializable isolation", "eReferral", "electronic referral", "specialist wait time", "healthcare information system Kazakhstan", "Django FastAPI Flask benchmark" |
| **Period** | Core sources 2021–2025; earlier works kept only if they are foundational reviews or describe a technology the project uses directly |
| **Included** | Peer-reviewed journal articles, conference papers and systematic reviews that inform a concrete requirement or design decision; official and news sources only for facts about Kazakhstan that are not covered by research |
| **Excluded** | Blog posts, vendor marketing, patents, sources without accessible abstract or metadata |

---

## 3.2 Structured analysis of scientific publications

| # | Source | Type, year | Focus | Key finding | Limitation | Use in project |
|---|--------|-----------|-------|-------------|------------|----------------|
| S1 | Ala, Chen — *J. Healthcare Engineering* | Review, 2022 | Appointment scheduling research 2000–2021 (150+ papers) | Groups the problem by patient flow, patient preferences, and random arrival and service times; patient preferences are an established factor in scheduling models | Mostly models for clinic operations, not citizen-facing booking portals | Justifies including time-of-day convenience in the slot score (F4) |
| S2 | Ahmadi-Javid, Jalali, Klassen — *European J. Operational Research* | Review, 2017 (foundational) | Optimization of outpatient appointment systems | Organizes decisions into strategic, tactical and operational levels and compares settings, models and solution methods | Older; clinic-side optimization, little on patient-side tools | Frames the slot algorithm as an operational-level decision |
| S3 | Yan, Huang, Kuo, Tang — *J. Management Science and Engineering* | Article, 2022 | Online scheduling with several physicians and patient choice | Patients may reject an undesirable slot or accept it with a higher no-show probability; good decisions depend on patient choice | Objective is clinic profit; simulated data | Recommending several ranked slots (top-5) instead of one; links slot convenience with no-show risk (F4, F20) |
| S4 | Ports, Grittner — *Proc. VLDB Endowment* | Conference paper, 2012 (foundational) | Serializable transactions in PostgreSQL | Describes PostgreSQL's serializable isolation; performance only slightly below snapshot isolation and better than two-phase locking on read-heavy workloads | General database work, not healthcare | Background for the transaction level of double-booking protection (NFR-D) |
| S5 | Dantas, Fleck, Cyrino Oliveira, Hamacher — *Health Policy* | Systematic review, 2018 (foundational) | No-show rates and determinants, 105 studies | Average no-show rate of the order of 23 % (13 % in Oceania to 43 % in Africa); determinants include long lead time, prior no-shows and distance to the clinic | Pre-2018 studies; rates differ strongly between settings | Quantifies problem P3; first features for the no-show model (F20) |
| S6 | Carreras-García, Delgado-Gómez, Llorente-Fernández, Arribas-Gil — *Entropy* | Systematic review, 2020 (foundational) | Models and predictors for no-show prediction | Maps which predictors and model families are used in no-show studies | Predates most recent ML work | Feature selection and evaluation metrics (F20) |
| S7 | Salazar, Parreira, Fernandes, Leithardt — *Information* | Systematic review, 2022 | Machine learning for no-show prediction | Reviews ML techniques and the factors most often used | Results depend on dataset and setting | Comparing logistic regression with tree-based models (F20) |
| S8 | Toffaha et al. — *Intelligence-Based Medicine* | Review, 2025 | ML no-show prediction, 52 publications 2010–2025 | Logistic regression is the most common model (68 % of studies); more complex models are gaining ground | Few real deployments | Logistic regression as the baseline; check whether complex models add value (F20) |
| S9 | Oikonomidi, Norman, McGarrigle, Stokes, van der Veer, Dowding — *JAMIA* | Rapid systematic review, 2023 | Interventions acting on predicted no-show risk (7 RCTs, 1 non-RCT) | Model-targeted text reminders reduce no-shows (high certainty); targeted phone reminders and navigators probably help; overbooking uncertain | Targeted vs non-targeted reminders not compared; little on cost and equity | Extra reminder for high-risk patients; baseline "remind everyone" (F15, F20) |
| S10 | Azamar-Alonso, Costa, Huebner, Tarride — *ClinicoEconomics and Outcomes Research* | Scoping review, 2019 (foundational) | Effectiveness and cost-effectiveness of eReferral systems | Evidence that eReferral improves communication and reduces wait times is positive but limited; economic evaluations are needed | Few economic studies; English only | Supports the referral module (F8–F10) and measuring time from referral to visit |
| S11 | Naimer et al. — *BMC Primary Care* | Mixed-methods study, 2022 | Wait time from primary care referral to specialist, 7141 referrals | Wait times can be extracted from records; clinicians find wait time reports useful for choosing the timeliest specialist | Two practices in Canada | Supports showing waiting time when choosing a specialist and the metric "days from referral to visit" (F4, v2 patient route) |
| S12 | Yermukhanova, Buribayeva, Abdikadirova, Tursynbekova, Kurganbekova — *J. Prev. Med. Public Health* | Expert assessment and SWOT, 2022 | Effectiveness of health information systems in polyclinics in Aktobe, Kazakhstan (40 experts) | Main gain is administrative control and document flow; no reduction in some financial costs | One region; expert opinions | Local context: digital systems exist, but benefits for patients are not automatic (problem statement) |
| S13 | Bednarz, Miłosz — *J. Computer Sciences Institute* | Benchmark, 2025 | Django REST Framework vs Flask vs FastAPI | FastAPI is faster in most scenarios; ORM use adds overhead even outside database work | Synthetic CRUD tests | Technology decision 1: speed is a known cost of Django, checked against NFR-P |
| S14 | Malatjie, Faniso-Myaka — *MATEC Web of Conferences* | Conference paper, 2025 | HTTP request handling in Flask, Django, FastAPI | Compares speed, scalability and ease of implementation and gives recommendations per scenario | Conference paper; general workloads | Technology decision 1 |

Recent (2021–2025): S1, S3, S7, S8, S9, S11, S12, S13, S14. Foundational: S2, S4, S5, S6, S10.

---

## 3.3 Official, news and industry sources

These sources are used only for facts about Kazakhstan and existing systems (sections 2 and 4), not as research evidence.

| # | Source | Used for |
|---|--------|----------|
| N1 | Inform.kz — statistics of online medical services in Kazakhstan | Scale of online booking in the country |
| N2 | BES.media — Ministry of Health on limits of booking in polyclinics | Patients cannot always book on their own even when schedules exist |
| N3 | gov.kz — public service "Appointment with a doctor" | Description of the eGov booking service |
| N4 | Zakon.kz, 2018 — digitalization of medicine, Damumed | Online booking in the Damumed app |
| N5 | Eurasianet, 2020 — Damumed case | Data quality problems in appointment records |
| N6 | Astana Hub — DOQ.kz | Private aggregator: doctor search, free slots, reviews |
| N7 | Forbes.kz — MedElement | Private portal: clinic search, booking, patient cabinet |

---

## 3.4 Critical synthesis

**What is already known.**
1. Appointment scheduling is a mature research area, mostly from the clinic's point of view; patient preferences for doctor and time are an accepted part of scheduling models (S1–S3).
2. No-shows affect about a quarter of appointments on average and are linked to long lead times, previous no-shows and distance (S5). Prediction models are well studied, with logistic regression as the most common baseline and tree-based models as the main alternative (S6–S8). Reminders targeted at high-risk patients are the best-supported intervention (S9).
3. Electronic referrals improve communication and can shorten waits, but the evidence is limited (S10); information about waiting times helps choose the timeliest specialist (S11).
4. In Kazakhstan, health information systems mainly improved administrative control, while some costs did not fall (S12); online booking exists at national scale but does not always let patients book on their own (N1, N2).
5. Among Python frameworks, FastAPI is faster than Django in benchmarks, and an ORM adds overhead (S13, S14).

**Limitations the project takes into account.**
- Scheduling models are usually evaluated on simulated data and optimize clinic metrics (S1–S3). → We evaluate the slot algorithm on synthetic data too, but against patient-side metrics (waiting time, distance) and a simple "earliest slot" baseline.
- No-show models depend on the setting where they were trained (S7, S8). → The model trained on a public dataset is used only for experiments; inside the system it works on synthetic data, and this is stated as a limitation.
- The added value of targeting reminders over reminding everyone is unproven (S9). → Targeted reminders are compared with reminders sent to everyone.
- Evidence on eReferral comes mainly from other countries (S10, S11). → Problems with referrals in Kazakhstan are verified through interviews (section 2).
- Framework benchmarks use synthetic workloads (S13, S14). → Performance is checked with our own load test against NFR-P.

**Gap.** In the sources reviewed, slot selection, booking correctness, referrals and no-shows are studied separately. We did not find a patient-facing system that ranks free slots across doctors by waiting time, distance and convenience, controls booking with specialists by electronic referrals and acts on no-show risk. This combination, in the context of Kazakhstan, is the focus of the project.

---

## 3.5 References

**Scientific publications**

- [S1] A. Ala, F. Chen, "Appointment scheduling problem in complexity systems of the healthcare services: a comprehensive review," *J. Healthcare Engineering*, vol. 2022, art. 5819813, 2022. doi:10.1155/2022/5819813
- [S2] A. Ahmadi-Javid, Z. Jalali, K. J. Klassen, "Outpatient appointment systems in healthcare: a review of optimization studies," *European J. Operational Research*, vol. 258, no. 1, pp. 3–34, 2017. doi:10.1016/j.ejor.2016.06.064
- [S3] C. Yan, G. Q. Huang, Y.-H. Kuo, J. Tang, "Dynamic appointment scheduling for outpatient clinics with multiple physicians and patient choice," *J. Management Science and Engineering*, vol. 7, no. 1, pp. 19–35, 2022. doi:10.1016/j.jmse.2021.02.002
- [S4] D. R. K. Ports, K. Grittner, "Serializable Snapshot Isolation in PostgreSQL," *Proc. VLDB Endowment*, vol. 5, no. 12, pp. 1850–1861, 2012. doi:10.14778/2367502.2367523
- [S5] L. F. Dantas, J. L. Fleck, F. L. Cyrino Oliveira, S. Hamacher, "No-shows in appointment scheduling — a systematic literature review," *Health Policy*, vol. 122, no. 4, pp. 412–421, 2018. doi:10.1016/j.healthpol.2018.02.002
- [S6] D. Carreras-García, D. Delgado-Gómez, F. Llorente-Fernández, A. Arribas-Gil, "Patient no-show prediction: a systematic literature review," *Entropy*, vol. 22, no. 6, art. 675, 2020. https://pmc.ncbi.nlm.nih.gov/articles/PMC7517206/
- [S7] L. H. A. Salazar, W. D. Parreira, A. M. R. Fernandes, V. R. Q. Leithardt, "No-show in medical appointments with machine learning techniques: a systematic literature review," *Information*, vol. 13, no. 11, art. 507, 2022. doi:10.3390/info13110507
- [S8] K. M. Toffaha et al., "Predicting patient no-shows using machine learning: a comprehensive review and future research agenda," *Intelligence-Based Medicine*, art. 100229, 2025. https://khazna.ku.ac.ae/en/publications/predicting-patient-no-shows-using-machine-learning-a-comprehensiv/
- [S9] T. Oikonomidi, G. Norman, L. McGarrigle, J. Stokes, S. N. van der Veer, D. Dowding, "Predictive model-based interventions to reduce outpatient no-shows: a rapid systematic review," *J. Am. Med. Inform. Assoc.*, vol. 30, no. 3, pp. 559–569, 2023. doi:10.1093/jamia/ocac242
- [S10] A. Azamar-Alonso, A. P. Costa, L.-A. Huebner, J.-E. Tarride, "Electronic referral systems in health care: a scoping review," *ClinicoEconomics and Outcomes Research*, vol. 11, pp. 325–333, 2019. doi:10.2147/CEOR.S195597
- [S11] M. S. Naimer et al., "Specialist wait time reporting using family physicians' electronic medical record data: a mixed method study of feasibility and clinical utility," *BMC Primary Care*, 2022. doi:10.1186/s12875-022-01679-x
- [S12] L. Yermukhanova, Z. Buribayeva, I. Abdikadirova, A. Tursynbekova, M. Kurganbekova, "SWOT analysis and expert assessment of the effectiveness of the introduction of healthcare information systems in polyclinics in Aktobe, Kazakhstan," *J. Prev. Med. Public Health*, vol. 55, no. 6, pp. 539–548, 2022. doi:10.3961/jpmph.22.360
- [S13] B. Bednarz, M. Miłosz, "Benchmarking the performance of Python web frameworks," *J. Computer Sciences Institute*, vol. 36, pp. 336–341, 2025. https://ph.pollub.pl/index.php/jcsi/article/view/7738
- [S14] P. G. Malatjie, Z. Faniso-Myaka, "Comparative analysis of HTTP request handling in different Python frameworks," *MATEC Web of Conferences*, vol. 417, art. 06017, 2025. doi:10.1051/matecconf/202541706017

**Official, news and industry sources**

- [N1] "Более 190 млн медицинских услуг в Казахстане оказали онлайн," Inform.kz. https://www.inform.kz/ru/bolee-190-mln-meditsinskih-uslug-v-kazahstane-okazali-onlayn-16bce7a6
- [N2] "К врачу не записаться: минздрав выявил ограничения в поликлиниках Казахстана," BES.media. https://bes.media/news/k-vrachu-ne-zapisatsya-minzdrav-vyyavil-ogranicheniya-v-poliklinikah-kazahstana/
- [N3] "Запись на прием к врачу," gov.kz. https://www.gov.kz/services/3177
- [N4] "Доступно и прозрачно: как продвигается цифровизация медицины в Казахстане," Zakon.kz, 2018. https://www.zakon.kz/tekhno/4944837-dostupno-i-prozrachno-kak-prodvigaetsya.html
- [N5] A. Kumenov, "Kazakhstan: Medical app mired in corruption scandal," Eurasianet, 7 Aug. 2020. https://eurasianet.org/kazakhstan-medical-app-mired-in-corruption-scandal
- [N6] "Казахстанский стартап по поиску врачей выходит на рынок Турции и Испании," Astana Hub. https://astanahub.com/en/article/kazakhstanskii-startap-po-poisku-vrachei-vykhodit-na-rynok-turtsii-i-ispanii
- [N7] "Казахстанский медицинский онлайн-проект выходит на рынок России," Forbes.kz. https://forbes.kz/articles/kazahstanskiy_meditsinskiy_onlayn-proekt_vyihodit_na_ryinok_rossii
