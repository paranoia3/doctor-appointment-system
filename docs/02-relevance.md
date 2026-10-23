# 2. Evidence of Relevance

Version 1.0, 23 October 2026.

This section shows what evidence the requirements are based on and separates **verified findings** from **assumptions**. It has three parts: evidence from public sources, assumptions, and interviews with users.

## 2.1 Verified evidence from public sources

| # | Finding | Source | Supports |
|---|---------|--------|----------|
| V1 | Online medical services, including booking, are used at national scale in Kazakhstan | [N1] | Context |
| V2 | The Ministry of Health reported that in some organizations schedules exist, but patients cannot always book on their own | [N2] | P1 |
| V3 | An expert study of health information systems in polyclinics of Aktobe found that the main gains were administrative, while some costs did not fall | [S12] | P1 (benefits for patients are not automatic) |
| V4 | Appointment records in Damumed had data quality problems (2020) | [N5] | P1 |
| V5 | Private aggregators (DOQ.kz, MedElement) compete on free time slots, catalogs and reviews | [N6], [N7] | Section 4 |
| V6 | No-shows affect about 23 % of appointments on average and are linked to long lead time, previous no-shows and distance | [S5] | P3, F15, F20 |
| V7 | Reminders targeted at high-risk patients reduce no-shows | [S9] | F15, F20 |

Sources [S…] and [N…] are listed in [`03-literature.md`](03-literature.md#35-references).

## 2.2 Assumptions (to be verified by interviews)

| # | Assumption | Requirements that depend on it |
|---|------------|--------------------------------|
| A1 | Patients lose significant time choosing a doctor and a free slot manually | F3, F4 |
| A2 | Distance to the clinic matters to patients when choosing between slots | F4, F6 |
| A3 | Patients find it hard to book a specialist after a referral; steps and validity are not clear | F8–F11 |
| A4 | Doctors lack a convenient view of the patient's referrals and visit history | F12 |
| A5 | Clinics lose capacity because of no-shows and have no simple way to reuse freed slots | F7, F15, F19, F20 |

## 2.3 Interviews

**Method.** Semi-structured interviews of 10–15 minutes, by phone or in person, in Russian, following the guide in [`interviews/guide.md`](interviews/guide.md). Each participant gave oral consent after the consent statement was read. Notes were taken during the conversations and later summarized; the protocols are summaries, not verbatim transcripts. No names, workplaces or contact details are stored.

**Participants (N = 6: 3 medical staff, 3 patients).**

| # | Role | Age group | Date | Format | Protocol |
|---|------|-----------|------|--------|----------|
| 1 | Doctor, narrow specialist (neurologist) | — | 23.10.2026 | Phone, 12 min | [`01-neurologist.md`](interviews/01-neurologist.md) |
| 2 | General practitioner | — | 23.10.2026 | Phone, 15 min | [`02-gp.md`](interviews/02-gp.md) |
| 3 | Senior registry staff / nurse | — | 23.10.2026 | Phone, 11 min | [`03-registry.md`](interviews/03-registry.md) |
| 4 | Patient, student | 18–25 | 23.10.2026 | In person, 10 min | [`04-patient-20.md`](interviews/04-patient-20.md) |
| 5 | Patient, employed | 26–45 | 23.10.2026 | Phone, 12 min | [`05-patient-35.md`](interviews/05-patient-35.md) |
| 6 | Patient who also books for a 74-year-old parent | 46–60 | 23.10.2026 | Phone, 14 min | [`06-patient-48.md`](interviews/06-patient-48.md) |

**Findings.** Each finding shows how many participants mentioned it and which assumption it confirms.

| # | Finding | Mentioned by | Assumption | Status | Requirement |
|---|---------|--------------|------------|--------|-------------|
| I1 | Freed time after no-shows and cancellations is lost; it does not reach patients who need it (2–4 of 15 patients per day do not come, as reported by a specialist) | 5 of 6 (#1, #2, #3, #4, #5) | A5 | **Verified** | F7, F15, F19, F20 |
| I2 | Patients and registry staff want a waitlist with automatic offers of freed slots; today "book me earlier" requests are handled by phone or by refreshing the app | 3 of 6 (#3, #5, #1) | A5 | **Verified** | F7 |
| I3 | Referrals expire (30 days) while patients wait for a free slot with a specialist; this is found out only at the visit, and the patient has to start again | 3 of 6 (#1, #6, #2) | A3 | **Verified** | F9, F10, F11 |
| I4 | Free time with specialists is hard to find; slots are taken weeks ahead (1 week to 1 month from referral to visit) | 3 of 6 (#2, #5, #6) | A1, A3 | **Verified** | F4, F7 |
| I5 | Double bookings happen when registry staff and the app book the same slot at the same moment | 1 of 6 (#2, medical worker) | — (new) | **Verified** (medical worker) | NFR-D1, F5 |
| I6 | When a doctor falls ill, appointments are cancelled manually and patients are not always informed | 2 of 6 (#3, #6) | — (new) | **Verified** | F18 |
| I7 | Cancelling an appointment is inconvenient, so patients do not cancel; a confirmation or a one-tap cancel in the reminder would help | 2 of 6 (#4, #1) | A5 | **Verified** | F15 |
| I8 | Booking requires too many steps even for one's own doctor | 1 of 6 (#4) | A1 | Assumption | F4, UX |
| I9 | Older patients cannot book on their own; relatives book for them | 1 of 6 (#6) | — (new) | Assumption | F2, NFR-U1 |
| I10 | About 30 % of patients still book through the registry or call center | 1 of 6 (#2, medical worker) | — (new) | **Verified** (medical worker) | Registry booking on behalf of patients |
| I11 | Distance to the clinic was not named as a criterion; one participant mentioned a long trip across the city | 1 of 6 (#6), indirect | A2 | Assumption (not confirmed) | F4, F6 |
| I12 | Doctors lack a convenient view of referral and visit history | 0 of 6 | A4 | Not discussed | F12 |

A finding is marked **verified** when it was mentioned by at least two participants or confirmed by a medical worker; otherwise it stays an **assumption**.

## 2.4 Conclusion

**Confirmed.** The interviews confirm A5 (lost capacity because of no-shows and no reuse of freed slots), A3 (problems with referrals while waiting for a specialist) and, partly, A1 (time spent finding free time). The strongest signal is the lack of a waitlist: it was raised independently by a specialist, the registry and patients.

**Not confirmed.** A2 (distance as a choice criterion) was not named by participants and stays an assumption; the distance component of the slot score will be evaluated experimentally (E1) rather than taken as a user need. A4 (doctor's view of history) was not discussed.

**New findings.** Double bookings between the registry and the app (I5), unannounced cancellations when a doctor is ill (I6), the share of registry bookings (I10) and the needs of older patients (I9).

**Proposed changes to requirements** (to be agreed with the supervisor):
- raise F7 "Waitlist" from v1 to **MVP**;
- raise F18 "Automatic rescheduling when a doctor is ill" from v2 to **v1**, with notification of affected patients;
- extend F15: the reminder 24 hours before the visit asks to **confirm or cancel with one tap**;
- add a requirement for **registry staff to book on behalf of a patient** in the same system, so that registry and online bookings share one protection against double booking;
- add a v2 requirement to **limit active bookings** for patients with repeated no-shows.

**Limitations.** The sample is small (6 participants) and all interviews were conducted on one day; protocols are summaries of notes. Findings are used to set priorities, not as statistically representative results.
