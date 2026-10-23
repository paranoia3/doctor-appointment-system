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
| C1 Online self-booking | ✔ [N4] | ✔ — application, digital signature required [N3; S-eGov-1, 2] | ? | ✔ [N6] | ✔ [N7] | MVP |
| C2 Free time slots visible per doctor | ? | ✖ — date chosen blindly; doctors and periods appear only after a date is chosen [S-eGov-3, 5] | ? | ✔ [N6] | ? | MVP |
| C3 Automatic nearest-slot recommendation across doctors | ? | ✖ [S-eGov-3, 5] | ? | ? | ? | MVP (top-5 ranking) |
| C4 Distance-aware ranking or map | ? | ✖ — only the clinic of attachment, no choice [S-eGov-4] | ? | ? | ? | v1 |
| C5 Electronic referral tied to booking | ? | ✖ — only family doctor and obstetrician-gynecologist can be booked [S-eGov-4] | ? | ? | ? | MVP |
| C6 Automatic reminders | ? | ? | ? | ? | ? | MVP |
| C7 Waitlist or live queue | ? | ✖ — other doctors "in the order of the live queue" at the clinic [S-eGov-2] | ? | ? | ? | v1 |
| C8 No-show prediction | ? | ✖ | ? | ? | ? | v2 |
| C9 Patient reviews of doctors | ? | ✖ | ? | ✔ [N6] | ✔ [N7] | Not planned |

Date of hands-on tests: 23.10.2026;

Screenshots (personal data removed): [`screenshots/existing-systems/`](screenshots/existing-systems/). S-eGov-1 … S-eGov-5 refer to files `egov-01-…png` … `egov-05-…png`.

## 4.3 Notes on the evidence

- **Damumed.** Online booking and home visit requests were introduced in the mobile app [N4]. Data quality problems with appointment records were reported in 2020 [N5].
- **eGov.kz** (service P702 "Appointment with a doctor", tested 23.10.2026 with a real patient account, stopped before submitting the application). The patient fills a form: date, clinic, service, doctor, period. The clinic is fixed to the clinic of attachment and cannot be changed. Only two services were offered: "family doctor" and "consultation: obstetrician-gynecologist". The date has to be chosen first, without seeing availability: for 25.10 the portal answered "the selected date is unavailable, choose another date or check the work schedule"; for 27.10 and 29.10 the lists of doctors and periods were empty. The service page says that no digital signature is needed, while the form shows "digital signature required". The result is a status notification in the personal cabinet, not an instant confirmation; doctors not in the list are seen "in the order of the live queue".
- **Mediker.** A medical assistance provider with its own network of medical centers, coordinators and family doctors ([1C-Bit client profile](https://www.1cbit.kz/company/clients/422593/)). Its digital booking features were not found in public sources, so all of its cells stay open until the hands-on test.
- **DOQ.kz.** A private service for finding doctors with free time slots and patient reviews [N6].
- **MedElement.** Offers clinic search, online booking, a patient cabinet and a clinic information system [N7].

References [N…] are listed in [`03-literature.md`](03-literature.md#35-references).

## 4.4 Why a new solution is needed

Among the confirmed features, the systems allow online booking with a chosen doctor, and the private portals show free time and reviews. None of the reviewed sources confirms that any of these systems:

1. recommends the nearest suitable slot **across doctors and clinics** by waiting time and distance (C3, C4);
2. ties an **electronic referral** to booking with a specialist (C5);
3. acts on the **risk of a no-show** or offers freed slots to a waitlist (C7, C8).

If the hands-on test confirms these gaps, they define the project's contribution. If a system turns out to have one of these features, the conclusion and the scope are updated accordingly.
