# 1. Project Proposal

| | |
|---|---|
| **Title** | Development of an online doctor appointment system with electronic referrals and selection of the nearest free time |
| **Team** | Yerkegali Yergali — backend developer; Aigerim Askarova — frontend developer |
| **Supervisor** | Сарсенова Жибек Нуржановна (Sarsenova Zhibek Nurzhanovna) |
| **Project type** | Team diploma project (2 students) |
| **Version** | 1.0, 20 October 2026 |

---

## 1.1 Problem statement

Online booking of outpatient appointments already exists in Kazakhstan: the Damumed mobile app, the eGov service "Appointment with a doctor" and private aggregators such as DOQ.kz and MedElement. However, these services are organized around **one doctor in one clinic**: the patient chooses a doctor first and then looks for free time in that doctor's schedule. Our preliminary analysis identifies three problems that this model leaves open:

| # | Problem | Consequence |
|---|---------|-------------|
| P1 | **Finding the nearest suitable appointment is manual.** To find the earliest convenient time for a specialty, the patient has to open the schedules of several doctors and clinics one by one. Waiting time, distance to the clinic and convenient time of day are not considered together. | Patients spend time searching, accept a later or more distant appointment than necessary, or give up and book by phone. |
| P2 | **Referrals are not connected to booking.** After a referral to a specialist (often with tests required first), the patient books each step separately and has to plan the order and timing alone. | Extra visits, gaps between steps, expired referrals and longer time from referral to the specialist visit. |
| P3 | **Missed appointments are not handled.** When a patient does not come, the slot is lost; there is no mechanism to predict the risk of a no-show or to offer the freed slot to someone else. | Lost doctor capacity and longer waiting times for other patients. |

The Ministry of Health has publicly acknowledged that in some organizations schedules exist but patients cannot always book on their own ([BES.media](https://bes.media/news/k-vrachu-ne-zapisatsya-minzdrav-vyyavil-ogranicheniya-v-poliklinikah-kazahstana/)), and research on outpatient care shows that no-shows are common and that targeted reminders reduce them (Oikonomidi et al., 2023; see [`03-literature.md`](03-literature.md)).

**Evidence status.** P1–P3 are working hypotheses. They are being verified in two ways: interviews with patients, doctors and registry staff ([`02-relevance.md`](02-relevance.md)) and a hands-on comparison of existing systems ([`04-existing-systems.md`](04-existing-systems.md)). Where the evidence does not confirm a problem, the scope will be adjusted.

---

## 1.2 Target users

| Role | Who | Main need |
|------|-----|-----------|
| **Patient** | Any registered adult; can also book for family members (child, parent) | Get a suitable appointment quickly, without searching doctor by doctor; know what to do with a referral |
| **Doctor** | Clinic staff | See the day's schedule and the patient's history; issue referrals that the patient can act on immediately |
| **Clinic administrator** | Registry staff or clinic manager | Manage doctors, rooms and schedules; see doctor workload and no-show rates |
| **Super administrator** | System developers | Manage clinics, specialties, service types and users |

---

## 1.3 Aim

To design, implement and experimentally evaluate a web system in which a patient books an outpatient appointment in 1–2 minutes: the system recommends the best free slots across doctors and clinics, electronic referrals control and simplify booking with specialists, and missed appointments are reduced through reminders and a waitlist.

---

## 1.4 Objectives

| # | Objective | Result |
|---|-----------|--------|
| O1 | Analyze existing booking systems in Kazakhstan and collect evidence from users | Interview results, comparison table of 5 systems |
| O2 | Specify requirements and design the architecture, data model and REST API | Requirements with acceptance criteria, diagrams, Swagger contract |
| O3 | Develop the nearest-slot recommendation algorithm (waiting time, distance, time-of-day convenience) | Algorithm, implementation, experiment against the "earliest slot" baseline |
| O4 | Implement booking with protection against double booking under concurrent requests | Booking module, load test |
| O5 | Implement electronic referrals with a lifecycle and referral-gated booking for narrow specialists | Referral module with automatic status changes |
| O6 | Implement supporting modules: reminders, waitlist, live queue, analytics; if time permits, patient route and no-show prediction | Working modules (see scope) |
| O7 | Test the system and evaluate the results against the targets below | Test report, experiment results |

---

## 1.5 Expected outcomes

All targets are verified in the evaluation plan ([`09-evaluation-plan.md`](09-evaluation-plan.md)).

| Outcome | Metric | Target |
|---------|--------|--------|
| Better slot recommendations | Mean waiting time and mean distance of the chosen slot, compared with the "earliest slot" baseline on the same synthetic data | Lower combined score than the baseline |
| Fast booking for patients | Time to complete a booking in usability sessions | 1–2 minutes |
| Fast search | Response time of the nearest-slot search, 100 doctors, 14-day horizon | ≤ 1 s |
| API performance | 95th percentile response time with 200 concurrent users | ≤ 500 ms |
| No double booking | 100 simultaneous requests for the same slot | Exactly 1 successful booking |
| Referral rules enforced | Bookings with a narrow specialist without an active referral | 100 % rejected (automated tests) |
| Code quality | Backend test coverage | ≥ 70 % |
| Reproducible deployment | Start of the whole system | One Docker Compose command |
| *(v2)* Shorter referral chains | Number of visits and total days from referral to specialist, compared with step-by-step manual booking | Fewer visits and days |
| *(v2)* No-show prediction | ROC-AUC, Recall, F1 of logistic regression, Random Forest, XGBoost | Best model selected by experiment |

---

## 1.6 Contribution

If the comparison of existing systems confirms the gaps described in P1–P3, the project contributes a combination that we have not found in the systems and sources reviewed so far:

1. **Nearest-slot recommendation across doctors and clinics** — the patient specifies a specialty instead of a doctor, and the system ranks free slots by a weighted score of waiting time, distance and time-of-day convenience.
2. **Referral-aware booking** — a referral issued by a doctor directly controls which specialists the patient can book and is closed automatically after the visit; in v2, the system builds the whole route "test → result → specialist".
3. **No-show handling** — reminders, a waitlist that receives freed slots, and (in v2) a risk model that triggers an additional reminder.

---

## 1.7 Engineering complexity

- **Concurrency.** Several patients may try to book the same slot at the same moment. Correctness is guaranteed on three levels: a temporary hold in Redis, a database transaction with row locking (`SELECT … FOR UPDATE`) and a PostgreSQL exclusion constraint that forbids overlapping appointments of one doctor.
- **Multi-criteria search.** Slots are generated from schedule templates for 14 days, minus breaks, vacations, holidays and existing bookings, then scored and ranked within 1 second.
- **State machines and scheduled jobs.** Referral statuses (*Issued → Used → Closed*, *Expired*, *Cancelled*), reminders and waitlist offers change automatically by events and by schedule (Celery).
- **Real-time updates.** The live queue pushes the patient's position and estimated time over WebSocket.
- **Security of medical data.** Role-based access, JWT, an audit log of access to medical data, IIN masking, compliance with the Law of the Republic of Kazakhstan on personal data.
- **Machine learning (v2).** Training and comparison of three models on a public dataset, and integration of the chosen model into the booking flow.

---

## 1.8 Scope

### In scope

| Priority | Purpose | Features |
|----------|---------|----------|
| **MVP** | First demo to the supervisor | Registration and login (IIN or phone, JWT); clinic and doctor catalog with filters; nearest-slot search; booking, cancellation, rescheduling; referrals with statuses and expiry; referral-gated booking; doctor schedule and patient card; email reminders; administration of doctors, rooms and schedules |
| **v1** | Pre-defense | Family profiles; clinic map with distance sorting; waitlist; "start / finish appointment" for doctors; live queue; analytics dashboard; Kazakh and Russian interface |
| **v2** | Final defense, if time permits | Patient route; Telegram bot; automatic rescheduling when a doctor is ill; no-show prediction; QR check-in |

### Out of scope

- Payments and insurance processing.
- Telemedicine (video consultations).
- Electronic health records beyond the visit history needed for booking.
- Integration with real national systems (Damumed, eGov). The demo uses synthetic data: 5 clinics, 30 doctors, 200 patients.

### Constraints and assumptions

- Team of two; working MVP by the end of December 2026, full version by the pre-defense.
- The no-show model is trained on a public dataset (Kaggle "Medical Appointment No Shows"); inside the system it runs on synthetic data with the same features.
- Distances are calculated from clinic and patient coordinates; real-time traffic is not considered.

---

## 1.9 Deliverables

1. Source code of the web application (backend, frontend, background jobs) in this repository.
2. Documentation: requirements, architecture and diagrams, prototype, evaluation plan (this `docs/` folder).
3. Experiment results: slot recommendation vs baseline, load test, and (v2) no-show models.
4. A demo scenario for the defense on synthetic data.
