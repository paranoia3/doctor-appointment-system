# 6. Requirements Specification

Version 1.0, 22 October 2026.

**Priorities.** **MVP** — minimum working version for the first demo (mandatory). **v1** — full version for the pre-defense (mandatory for the pre-defense). **v2** — extras for the final defense, implemented if time permits.

**Basis.** Each requirement is linked to the problem it addresses (P1–P3 from the proposal, [`01-proposal.md`](01-proposal.md)) and, where applicable, to a source from the literature review ([`03-literature.md`](03-literature.md)). Interview results ([`02-relevance.md`](02-relevance.md)) may change priorities.

**Acceptance criteria** are written so that each one can be checked by an automated test, a load test or a demo scenario. Values marked as targets are working values that will be confirmed with the supervisor.

---

## 6.1 Roles

| Role | Access |
|------|--------|
| Patient | Own profile and family profiles, own appointments and referrals |
| Doctor | Own schedule; cards of patients who have an appointment or a referral with this doctor |
| Clinic administrator | Doctors, rooms, schedules and analytics of own clinic only |
| Super administrator | All clinics, specialties, service types and users |

---

## 6.2 Functional requirements

| ID | Requirement | Priority | Basis |
|----|-------------|----------|-------|
| F1 | Registration and login by IIN or phone, JWT tokens, password recovery | MVP | — |
| F2 | Family profile: booking for a child or parent from one account | v1 | P1 |
| F3 | Catalog of clinics and doctors with filters: specialty, clinic, language of consultation | MVP | P1 |
| F4 | Nearest free slot search by specialty across all doctors | MVP | P1; S1, S3, S11 |
| F5 | Booking, cancellation and rescheduling with confirmation | MVP | P1 |
| F6 | Map of clinics, sorting by distance from the patient | v1 | P1; S5 |
| F7 | Waitlist: when a slot is freed, the first patient in the queue receives an offer | v1 | P3 |
| F8 | Doctor issues a referral: to a specialist, for a test, for an examination | MVP | P2; S10 |
| F9 | A narrow specialist can be booked only with an active referral | MVP | P2 |
| F10 | Referral statuses and validity period, automatic expiry | MVP | P2 |
| F11 | Patient route: the system builds the chain "test → result → specialist" | v2 | P2; S11 |
| F12 | Doctor's schedule for a day and a week, patient card with visit history | MVP | — |
| F13 | "Start appointment" and "Finish appointment" buttons for the doctor | v1 | — |
| F14 | Live queue: patient's position and estimated time, updated in real time | v1 | P3 |
| F15 | Email reminders 24 h and 2 h before the appointment | MVP | P3; S9 |
| F16 | Telegram bot: reminders, confirmation and booking | v2 | P3 |
| F17 | Administration: doctors, rooms, schedules, vacations | MVP | — |
| F18 | Automatic rescheduling of appointments when a doctor is ill | v2 | P1 |
| F19 | Analytics dashboard: doctor workload, no-show rate, popular specialties | v1 | P3 |
| F20 | No-show risk prediction and an additional reminder for high-risk appointments | v2 | P3; S5–S9 |
| F21 | Kazakh and Russian interface | v1 | — |
| F22 | QR check-in of the patient at the doctor's room | v2 | — |

Summary: 10 MVP, 7 v1, 5 v2.

---

## 6.3 Acceptance criteria — MVP

**F1. Registration and login**
- AC1.1 Registration with a valid 12-digit IIN or a phone number in the format +7XXXXXXXXXX creates an account; a second registration with the same IIN or phone is rejected with a clear message.
- AC1.2 An IIN that is not 12 digits or fails the control digit check is rejected.
- AC1.3 Successful login returns an access token valid for 15 minutes and a refresh token valid for 7 days; a request with an expired access token returns 401; the refresh token returns a new access token.
- AC1.4 Password recovery sends a one-time link to the registered email; the link works once and expires after 30 minutes (target).
- AC1.5 Passwords are stored only as hashes; no API response contains a password or hash.

**F3. Catalog with filters**
- AC3.1 Filtering by specialty, clinic and language returns only doctors that match all selected filters.
- AC3.2 If no doctor matches, the patient sees an empty-state message, not an error.

**F4. Nearest free slot**
- AC4.1 For a given specialty, the system returns at most 5 slots within the next 14 days, sorted by score (lower is better).
- AC4.2 No returned slot overlaps an existing appointment, a break, a vacation, a sick leave or a public holiday.
- AC4.3 If the patient has an active referral, only slots in clinics where the referral is valid are returned.
- AC4.4 If the patient chooses a preferred time of day, slots outside it receive a penalty and appear lower.
- AC4.5 With 100 doctors and a 14-day horizon, the response time is at most 1 second (see NFR-P1).
- AC4.6 If no slot is found, the patient sees a message (and, from v1, an offer to join the waitlist).

**F5. Booking, cancellation, rescheduling**
- AC5.1 Selecting a slot holds it for 5 minutes; during the hold, the slot is not offered to other patients.
- AC5.2 Confirming the booking creates an appointment with status *Booked*, and the slot no longer appears in search.
- AC5.3 If two patients confirm the same slot at the same time, exactly one booking succeeds and the other patient receives a message to choose another slot (see NFR-D1).
- AC5.4 Cancellation sets status *Cancelled* and makes the slot available in search again.
- AC5.5 Rescheduling is atomic: the old slot is released only if the new booking succeeds.
- AC5.6 A confirmation email is sent within 1 minute after booking, cancellation or rescheduling.

**F8. Issuing a referral**
- AC8.1 A doctor can issue a referral only to a patient who has an appointment with this doctor.
- AC8.2 A referral contains the target specialty or service, an ICD-10 diagnosis code and a validity period (30 days by default).
- AC8.3 The patient sees the new referral in the personal cabinet immediately after it is issued.

**F9. Referral-gated booking**
- AC9.1 Booking a specialty marked "referral required" without an active referral is rejected with an explanation.
- AC9.2 Booking with an active referral succeeds and changes the referral status to *Used*.
- AC9.3 One referral can be used for only one appointment.

**F10. Referral lifecycle**
- AC10.1 Statuses change only along the allowed transitions: *Issued → Used → Closed*, *Issued → Expired*, *Issued → Cancelled*.
- AC10.2 When the related appointment is finished, the referral becomes *Closed*.
- AC10.3 If the appointment booked with a referral is cancelled before the visit, the referral returns to *Issued* (if still valid).
- AC10.4 A scheduled job sets *Expired* for all referrals whose validity period has passed; it runs at least once a day.
- AC10.5 Only the issuing doctor can cancel a referral.

**F12. Doctor's schedule and patient card**
- AC12.1 The doctor sees appointments for the selected day or week, only their own.
- AC12.2 The patient card shows visit history only for patients who have an appointment or a referral with this doctor; for others the API returns 403.
- AC12.3 Each opening of a patient card is written to the audit log (see NFR-S3).

**F15. Reminders**
- AC15.1 For every appointment with status *Booked*, an email reminder is sent 24 hours and 2 hours before the start (tolerance ±5 minutes).
- AC15.2 No reminder is sent for cancelled appointments.
- AC15.3 Each reminder is sent at most once, even if the background job is restarted.

**F17. Administration**
- AC17.1 A clinic administrator can create, edit and deactivate doctors, rooms, weekly schedule templates and vacations of their own clinic only.
- AC17.2 After a vacation or sick leave is added, slots in that period are no longer offered.
- AC17.3 Existing appointments that fall into a new vacation are listed for the administrator to reschedule.

---

## 6.4 Acceptance criteria — v1

| ID | Acceptance criteria |
|----|---------------------|
| F2 | A patient can add family profiles; booking for a family member creates the appointment on that profile; family profiles are not visible to other users. |
| F6 | Clinics are shown on a map; sorting by distance orders clinics by straight-line distance from the patient's saved address. |
| F7 | When a slot is freed, the first matching patient in the waitlist receives an offer valid for 30 minutes (target); if it is declined or expires, the next patient receives it. |
| F13 | "Start" sets the appointment to *In progress*, "Finish" sets it to *Completed* and closes the related referral; patients who did not come are marked *No-show*. |
| F14 | After the doctor's action, the patient's position and estimated time update within 2 seconds without reloading the page; a patient sees only their own position. |
| F19 | For a selected period, the dashboard shows doctor workload (%), no-show rate (%) and top specialties; the numbers match a direct database query on test data. |
| F21 | All interface texts exist in Kazakh and Russian; the selected language is remembered for the user. |

---

## 6.5 Acceptance criteria — v2 (if time permits)

| ID | Acceptance criteria |
|----|---------------------|
| F11 | For a referral with preparatory steps, each step is scheduled not earlier than the end of the previous step plus the result readiness time; steps in one clinic are placed on one day when possible; "Book route" books all steps or none. |
| F16 | Through the bot, a patient can receive reminders, confirm an appointment and book a slot using the same API as the web app. |
| F18 | When a doctor's sick leave is added, the system offers each affected patient the nearest alternative slot with a doctor of the same specialty. |
| F20 | The selected model is compared with logistic regression by ROC-AUC, Recall and F1 on a held-out test set; for appointments above the risk threshold, an additional reminder with a confirmation request is sent; without confirmation the slot is offered to the waitlist. |
| F22 | Scanning the QR code at the room marks the patient as arrived and updates the live queue. |

---

## 6.6 Non-functional requirements

| ID | Category | Requirement | Target | How it is verified |
|----|----------|-------------|--------|--------------------|
| NFR-P1 | Performance | Nearest-slot search | ≤ 1 s with 100 doctors and a 14-day horizon | Load test (Locust) on synthetic data |
| NFR-P2 | Performance | API response time | 95 % of requests ≤ 500 ms with 200 concurrent users | Load test (Locust) |
| NFR-D1 | Data integrity | No double booking | 100 simultaneous requests for one slot → exactly 1 appointment | Concurrent load test + PostgreSQL exclusion constraint |
| NFR-S1 | Security | HTTPS, password hashing, JWT (access 15 min, refresh 7 days) | All endpoints except login and registration require a valid token | Automated API tests |
| NFR-S2 | Security | Role-based access | A patient sees only own data; a doctor only own patients; an administrator only own clinic | Automated permission tests for each role |
| NFR-S3 | Security | Audit log of access to medical data | Every view of a patient card or referral is logged: who, when, what | Automated test + log review |
| NFR-S4 | Privacy | Compliance with the Law of the Republic of Kazakhstan on personal data | IIN is masked in the interface (e.g. `9901******34`); consent is collected at registration | Demo and UI review |
| NFR-R1 | Reliability | Backups and error monitoring | Daily database backup; errors are reported to Sentry | Configuration review |
| NFR-U1 | Usability | Responsive layout | Works from 360 px width | Manual check on phone and desktop |
| NFR-U2 | Usability | Booking time | A new user books an appointment in 1–2 minutes | Usability sessions with 5 participants |
| NFR-C1 | Compatibility | Browsers | Latest Chrome, Safari, Firefox, Edge | Manual check, E2E tests in Playwright |
| NFR-M1 | Maintainability | Test coverage | Backend coverage ≥ 70 % | pytest-cov report in CI |
| NFR-M2 | Deployment | One-command start | `docker compose up` starts the whole system | Clean run on a new machine |
