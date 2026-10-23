# 9. Verification and Evaluation Plan

Version 1.0, 23 October 2026.

This plan describes how we will show that the project aim is achieved: which tests are run, which metrics are measured, under which conditions, and what each result is compared with. Requirements and acceptance criteria are in [`06-requirements.md`](06-requirements.md); expected outcomes are in [`01-proposal.md`](01-proposal.md#15-expected-outcomes).

---

## 9.1 Overview

| # | What is evaluated | Method | Baseline | Success criterion | Requirement |
|---|-------------------|--------|----------|-------------------|-------------|
| E1 | Quality of slot recommendations | Offline experiment on synthetic data | "Earliest free slot" | Lower combined score than the baseline; waiting time not worse by more than 10 % | F4 |
| E2 | Protection against double booking | Concurrent load test + ablation | Same test with protection levels switched off | Exactly 1 appointment in every run | F5, NFR-D1 |
| E3 | Performance | Load test (Locust) | — (absolute targets) | Search ≤ 1 s; 95 % of requests ≤ 500 ms at 200 users | NFR-P1, NFR-P2 |
| E4 | Functional correctness | Automated unit, API and E2E tests | — | All MVP acceptance criteria pass; backend coverage ≥ 70 % | F1–F17, NFR-M1 |
| E5 | Usability of booking | Usability sessions, 5 participants | The same task in an existing booking service | Median booking time ≤ 2 min; success rate ≥ 80 % | NFR-U2 |
| E6 *(v2)* | Patient route | Offline experiment on synthetic referral chains | Booking each step manually as early as possible | Fewer visits and fewer days from referral to specialist | F11 |
| E7 *(v2)* | No-show prediction | ML experiment on a public dataset | Logistic regression | Best model chosen by ROC-AUC with acceptable Recall | F20 |

---

## 9.2 Test data and environment

**Synthetic data.** A seeded generator (fixed random seed, so every run is reproducible) creates:
- 5 clinics with coordinates in one city;
- 30 doctors in 8 specialties, with weekly schedule templates, breaks, vacations and public holidays;
- 200 patients with home coordinates, age and booking history;
- existing appointments filling about 60 % of the slots in the next 14 days (occupancy is varied in E1).

For the performance target NFR-P1, the generator is scaled to 100 doctors.

**Environment.** All measurements run in Docker Compose on one machine; its CPU, memory and OS are recorded with the results. Each measurement is repeated at least 5 times; we report the mean and the 95th percentile.

**Where results are stored.** Generator, experiment scripts and raw results are committed to the repository (`src/ml/`, `tests/load/`); summary tables go to `docs/results/`.

---

## 9.3 E1. Slot recommendation vs "earliest slot"

**Question.** Does the weighted score (waiting time *T*, distance *D*, inconvenient-time penalty *P*) give better slots for patients than simply offering the earliest free slot?

**Setup.**
- 500 booking requests generated from the synthetic patients: random specialty, patient location and preferred time of day (morning / afternoon / evening / none).
- For each request, both methods choose a slot from the same set of free slots.
- Weights start at *w1* = 0.6, *w2* = 0.3, *w3* = 0.1; a grid of other weights is tested to show how sensitive the result is.
- Repeated for occupancy 40 %, 60 % and 80 %.

**Metrics.**
- Mean waiting time until the appointment (hours).
- Mean distance to the clinic (km).
- Share of slots inside the patient's preferred time of day (%).
- Combined score of the chosen slot.

**Analysis.** Paired comparison of the two methods on the same requests (Wilcoxon signed-rank test, significance level 0.05).

**Success.** The recommended slot has a lower combined score than the baseline, and the mean waiting time is not more than 10 % worse. The trade-off between waiting time and distance is shown in a chart for different weights.

---

## 9.4 E2. Double booking under concurrency

**Question.** Can two patients ever book the same slot?

**Setup.** Locust sends 100 simultaneous booking requests for one free slot from 100 different patients. The test is repeated 10 times. Then it is repeated with protection levels switched off one by one (ablation):

| Configuration | Expected result |
|---------------|-----------------|
| All three levels: Redis hold + row lock + exclusion constraint | Exactly 1 appointment |
| Without Redis hold | Exactly 1 appointment (more rejected requests at confirmation) |
| Without Redis hold and without row lock | Exactly 1 appointment (the constraint rejects the rest) |
| Without any protection | Duplicates possible — shows that the problem is real |

**Metrics.** Number of created appointments for the slot; number and type of rejected requests; response time.

**Success.** Exactly one appointment in every run with full protection.

---

## 9.5 E3. Performance

| Test | Load | Duration | Metric | Target |
|------|------|----------|--------|--------|
| Nearest-slot search | 100 doctors, 14-day horizon, 1 to 50 concurrent users | 5 min | Response time, 95th percentile | ≤ 1 s |
| Mixed API load | Ramp up to 200 concurrent users; scenario: search 50 %, view appointments 30 %, book or cancel 20 % | 10 min | 95th percentile response time; error rate | ≤ 500 ms; errors < 1 % |

If a target is missed, the slowest endpoints are profiled and the test is repeated after optimization; both results are reported.

---

## 9.6 E4. Functional testing

| Level | Tool | What is covered | Target |
|-------|------|-----------------|--------|
| Unit | pytest | Slot generation, scoring, referral status transitions, permission checks | Backend coverage ≥ 70 % |
| API / integration | pytest + DRF test client | Every MVP acceptance criterion in `06-requirements.md` has at least one test | 100 % of MVP criteria covered |
| Frontend unit | Vitest | Forms, validation, components of slot selection | Key components covered |
| End-to-end | Playwright | Scenarios: registration → search → booking → cancellation; doctor issues referral → patient books specialist | All scenarios pass |
| Continuous integration | GitHub Actions | All tests run on every pull request | A pull request is merged only when tests pass |

A traceability table "acceptance criterion → test name" is kept in `docs/results/` so that the committee can check each mandatory requirement.

---

## 9.7 E5. Usability sessions

**Participants.** 5 people who did not take part in development, of different ages; at least one is over 50.

**Tasks.**
1. Book the nearest available therapist.
2. Use an existing referral to book a specialist.
3. Cancel an appointment.

**Baseline.** Each participant first performs task 1 in an existing booking service they normally use (if available), then in our system.

**Metrics.** Time to complete each task, success rate, number of errors, System Usability Scale (SUS) questionnaire after the session.

**Success.** Median time for task 1 ≤ 2 minutes; at least 4 of 5 participants complete each task.

---

## 9.8 E6 (v2). Patient route

**Setup.** 50 synthetic referrals with preparatory steps (for example "blood test → result in 1 day → endocrinologist").
**Baseline.** Each step is booked separately at the earliest free time after the previous step's result is ready, without combining steps.
**Metrics.** Number of separate visits to clinics; total days from referral to the specialist visit.
**Success.** Fewer visits and fewer days on average than the baseline.

---

## 9.9 E7 (v2). No-show prediction

**Data.** Public dataset "Medical Appointment No Shows" (Kaggle, about 110 thousand appointments). Inside the system, the model works on synthetic data with the same features.
**Features.** Lead time between booking and visit, day of week, time of day, age, previous no-shows, distance to the clinic, reminder received.
**Method.** Stratified split 80 / 20; 5-fold cross-validation on the training part; class imbalance handled with class weights.
**Models.** Logistic regression (baseline), Random Forest, XGBoost.
**Metrics.** ROC-AUC (main), Recall and F1 for the "no-show" class, PR-AUC.
**Reminder policy.** With the chosen model and threshold, we report how many extra reminders would be sent and how many actual no-shows they would cover, compared with sending the extra reminder to everyone.
**Success.** The chosen model is better than logistic regression by ROC-AUC on the test set, or logistic regression is kept if the difference is negligible.

---

## 9.10 Threats to validity

| Threat | Effect | How we handle it |
|--------|--------|------------------|
| Synthetic data instead of real clinics | Results of E1, E2, E6 may differ in real use | Data generator and parameters are published; results are shown for several occupancy levels |
| Small usability sample (5 people) | Results are indicative only | Reported as qualitative findings together with the numbers |
| Dataset for E7 comes from another country | Model may not transfer to Kazakhstan | Stated as a limitation; the model is used only to demonstrate the approach |
| No real patients receive reminders | The effect of reminders on no-shows cannot be measured | We measure only prediction quality and reminder volume; a real effect requires a pilot in a clinic |
| One test machine | Absolute performance depends on hardware | Hardware is recorded; targets are checked on the same machine before and after changes |

---

## 9.11 Schedule

| When | What |
|------|------|
| Before the MVP demo (end of December 2026) | E4 for MVP requirements, E2, first run of E3 |
| Before the pre-defense | E1, E3 final run, E5 |
| Before the final defense (if v2 is implemented) | E6, E7 |
