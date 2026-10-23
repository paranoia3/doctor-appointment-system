# 4. Existing System Comparison

Version 1.0, 23 October 2026.

Five systems that patients in Kazakhstan use to book outpatient appointments are compared against nine criteria. The criteria come from the problems P1–P3 in the proposal ([`01-proposal.md`](01-proposal.md)) and the requirements ([`06-requirements.md`](06-requirements.md)).

## 4.1 Method

- **Systems.** Two state services (Damumed patient app, eGov service "Appointment with a doctor"), one private medical network (Mediker) and two private portals (DOQ.kz, MedElement / Doctor.kz). They cover the main ways patients book today.
- **Evidence.** Each cell is marked only when it is supported by a cited source or by a hands-on test.
- **Hands-on test.** Each system is opened with a patient account (or as a guest where registration is not possible), the booking scenario for a therapist is followed to the confirmation screen, and screenshots are saved to [`docs/screenshots/existing-systems/`](screenshots/existing-systems/) with the date of the test.

**Legend.** ✔ — available (source or screenshot); ✖ — not available (source or not found during the hands-on test); ? — not confirmed yet. A "?" does not mean that the feature is absent.

## 4.2 Comparison table

| Criterion | Damumed | eGov.kz service | Mediker | DOQ.kz | MedElement / Doctor.kz | This project |
|-----------|---------|-----------------|---------|--------|------------------------|--------------|
| Type | State medical information system with patient app | State portal service | Medical assistance company with own clinic network | Private booking marketplace | Private portal and clinic information system vendor | Academic prototype |
| C1 Online self-booking | ✔ — with own district doctor [N4; S-Dm-1, 3] | ✔ — application, digital signature required [N3; S-eGov-1, 2] | ✖ — "Book" opens a WhatsApp message to the call center [S-Mk-3, 4] | ✔ — guest booking with phone confirmation [N6; S-Dq-3] | ✔ — booking request form [N7; S-Me-2] | MVP |
| C2 Free time slots visible per doctor | ✔ — dates and times of the district doctor [S-Dm-1] | ✖ — date chosen blindly; doctors and periods appear only after a date is chosen [S-eGov-3, 5] | ✖ [S-Mk-4] | ✔ — free times for several days on each doctor card [N6; S-Dq-2] | ✖ — only a preference ("nearest days", "before / after lunch"), no concrete slots [S-Me-2] | MVP |
| C3 Automatic nearest-slot recommendation across doctors | ✖ — only own district doctor [S-Dm-1, 2] | ✖ [S-eGov-3, 5] | ✖ | ✖ — filters "today" and "date", no ranking by combined criteria [S-Dq-2] | ✖ — request for "nearest days", the clinic chooses [S-Me-2] | MVP (top-5 ranking) |
| C4 Distance-aware ranking or map | ✖ — only the clinic of attachment [S-Dm-1] | ✖ — only the clinic of attachment, no choice [S-eGov-4] | ✖ — list of centers by city [S-Mk-2] | Partly — "nearby" filter and map, not combined with waiting time [S-Dq-2] | Partly — clinic addresses and city filter [S-Me-1] | v1 |
| C5 Electronic referral tied to booking | ? — an "Active referrals" section exists, link to booking not tested [S-Dm-2] | ✖ — only family doctor and obstetrician-gynecologist can be booked [S-eGov-4] | ✖ | ✖ — private paid appointments | ✖ — private paid appointments | MVP |
| C6 Automatic reminders | ? | ? | ? | ? | ? | MVP |
| C7 Waitlist or live queue | ✖ — not found | ✖ — other doctors "in the order of the live queue" at the clinic [S-eGov-2] | ✖ | ✖ — not found | ✖ — not found | v1 |
| C8 No-show prediction | ✖ | ✖ | ✖ | ✖ | ✖ | v2 |
| C9 Patient reviews of doctors | ✖ — not found | ✖ | ✖ — not found | ✔ — reviews on doctor pages [N6; S-Dq-3] | ✔ — ratings and reviews of clinics [N7; S-Me-1] | Not planned |

Date of hands-on tests: 23.10.2026 (all five systems). Reminders (C6) could not be checked without completing a booking.

Screenshots (personal data removed): [`screenshots/existing-systems/`](screenshots/existing-systems/). S-eGov-N → `egov-0N-….png`, S-Dm-N → `damumed-0N.png`, S-Mk-N → `mediker-0N.png`, S-Dq-N → `doq-0N.png`, S-Me-N → `medelement-0N.png`.

## 4.3 Notes on the evidence

- **Damumed** (web version, patient account, tested 23.10.2026, stopped before confirming). The patient sees the clinic of attachment and the district doctor, a strip of dates and free times, and books with the district doctor; other clinics and doctors are not offered. The "Choose doctor and room" dialog lists only services of the same district doctor. An "Active referrals" section exists in the cabinet; whether a referral leads directly to booking was not tested. Online booking and home visit requests were introduced in the app in 2018 [N4]; data quality problems with appointment records were reported in 2020 [N5].
- **eGov.kz** (service P702 "Appointment with a doctor", tested 23.10.2026 with a real patient account, stopped before submitting the application). The patient fills a form: date, clinic, service, doctor, period. The clinic is fixed to the clinic of attachment and cannot be changed. Only two services were offered: "family doctor" and "consultation: obstetrician-gynecologist". The date has to be chosen first, without seeing availability: for 25.10 the portal answered "the selected date is unavailable, choose another date or check the work schedule"; for 27.10 and 29.10 the lists of doctors and periods were empty. The service page says that no digital signature is needed, while the form shows "digital signature required". The result is a status notification in the personal cabinet, not an instant confirmation; doctors not in the list are seen "in the order of the live queue".
- **Mediker.** A medical assistance provider with its own network of medical centers, coordinators and family doctors ([1C-Bit client profile](https://www.1cbit.kz/company/clients/422593/)). On the website (tested 23.10.2026) the patient chooses a medical center from a list by city; the "Book an appointment" button opens a prepared WhatsApp message to the call center. There is no online schedule or slot selection.
- **DOQ.kz** (tested 23.10.2026 as a guest). Doctors are listed by specialty with free times for several days on each card; filters include "today", "nearby" and date, and a map is available. Booking requires name and phone with SMS confirmation. Results are not ranked by a combination of waiting time and distance, and referrals are not part of the process (paid private appointments) [N6].
- **MedElement** (tested 23.10.2026 as a guest). Clinics are listed with ratings, reviews, addresses and prices. The booking form collects name, phone and e-mail and a time preference ("nearest days", "today", "before / after lunch"); concrete free slots are not shown, the clinic confirms the time [N7].

References [N…] are listed in [`03-literature.md`](03-literature.md#35-references).

## 4.4 Why a new solution is needed

The hands-on tests show that state services book only with the clinic of attachment (Damumed with visible slots of the district doctor, eGov without visible availability), Mediker books through a messenger, and private portals show free time (DOQ.kz) or accept requests (MedElement). None of the five systems:

1. recommends the nearest suitable slot **across doctors and clinics** by waiting time and distance (C3, C4);
2. ties an **electronic referral** to booking with a specialist (C5);
3. acts on the **risk of a no-show** or offers freed slots to a waitlist (C7, C8).

These gaps define the project's contribution. DOQ.kz comes closest on C2 and C4 (visible slots, "nearby" filter), which is why the project is evaluated against an "earliest slot" baseline and measures distance explicitly (experiment E1).
