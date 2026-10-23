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

**Method.** Semi-structured interviews of 10–20 minutes by phone or video, following the guide in [`interviews/guide.md`](interviews/guide.md). Participants gave consent; notes are anonymized ("Patient 1, 34 years"). Audio, if recorded, is kept outside the repository.

**Participants.**

| # | Role | Age group | Date | Format | Protocol |
|---|------|-----------|------|--------|----------|
| 1 | | | | | `interviews/01-….md` |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |

**Findings.** Each finding shows how many participants mentioned it and which assumption it confirms or rejects.

| # | Finding | Mentioned by (n of N) | Confirms / rejects | Status | Requirement |
|---|---------|----------------------|--------------------|--------|-------------|
| I1 | | | | Verified / assumption | |
| I2 | | | | | |
| I3 | | | | | |
| I4 | | | | | |
| I5 | | | | | |

A finding is marked **verified** when it was mentioned by at least two participants or confirmed by a medical worker; otherwise it stays an **assumption**.

## 2.4 Conclusion

_Written after the interviews: which assumptions were confirmed, which were rejected, and what changed in the requirements or priorities._
