# Online Doctor Appointment System with Electronic Referrals and Nearest-Slot Selection

A web system where a patient books a doctor's appointment in 1–2 minutes: the system finds the nearest suitable free slot across doctors and clinics, manages electronic referrals from doctor to doctor and reduces losses from missed appointments.

> **Official topic:** Development of an online doctor appointment system with electronic referrals and selection of the nearest free time.

---

## Table of Contents

1. [Problem Statement, Target Users, Expected Outcomes](#1-problem-statement-target-users-expected-outcomes)
2. [Team and Supervisor](#2-team-and-supervisor)
3. [Project Scope and Current Status](#3-project-scope-and-current-status)
4. [Pre-defense documentation](#4-pre-defense-documentation)
5. [Selected Technologies](#5-selected-technologies)
6. [Repository Structure](#6-repository-structure)
7. [Links](#7-links)
8. [Development Workflow](#8-development-workflow)
9. [Setup, Execution and Testing](#9-setup-execution-and-testing)

---

## 1. Problem Statement, Target Users, Expected Outcomes

### Problem statement

Online booking already exists in Kazakhstan, but it is organized around one doctor in one clinic. Our comparison of five systems ([`docs/04-existing-systems.md`](docs/04-existing-systems.md)) and interviews with three medical workers and three patients ([`docs/02-relevance.md`](docs/02-relevance.md)) show three problems:

- **Finding free time is manual.** State services book only with the clinic of attachment; patients refresh apps for days to catch a freed slot with a specialist.
- **Referrals are not connected to booking.** Referrals expire (30 days) while patients wait for a specialist, and this is found out only at the visit.
- **Missed appointments are not handled.** 2–4 of 15 patients per day do not come (reported by a specialist); freed time is lost and does not reach patients on a waitlist.

### Target users

| Role | Description |
|------|-------------|
| **Patient** | Any registered user; can also book for family members (child, parent) |
| **Doctor** | Clinic staff: views the schedule, starts/finishes appointments, issues referrals |
| **Clinic administrator** | Registry staff or manager: manages doctors, rooms, schedules, sees analytics |
| **Super admin** | System developers: manage clinics, specialties, service types and all users |

### Expected outcomes

- Booking in 1–2 minutes with automatic selection of the 5 best slots across doctors and clinics (waiting time, distance, time-of-day convenience), compared experimentally with an "earliest slot" baseline.
- No double booking: 100 concurrent requests for one slot produce exactly one appointment (Redis hold + row lock + PostgreSQL exclusion constraint).
- Electronic referrals with a lifecycle (*Issued → Used → Closed*, *Expired*, *Cancelled*); narrow specialists are bookable only with an active referral.
- Waitlist and reminders that return freed slots to patients who need them.
- *(v2, if time permits)* Patient route "test → result → specialist" and ML-based no-show prediction.

Full measurable targets: [`docs/01-proposal.md`](docs/01-proposal.md#15-expected-outcomes).

---

## 2. Team and Supervisor

| Member | GitHub | Role | Responsibilities |
|--------|--------|------|------------------|
| Yerkegali Yergali | `paranoia3` | Backend developer | Data model, API, slot algorithm, booking and double-booking protection, referrals, background jobs, live queue (server), ML no-show model, load tests |
| Aigerim Askarova | `aigerimaskarova11` | Frontend developer | Mockups and prototype, patient / doctor / administrator interfaces, map, live queue screen, analytics dashboard, KZ/RU localization, E2E tests |
| Both | — | — | Analysis of existing systems, interviews, API contract, test data, demo scenarios, report |

**Supervisor:** Сарсенова Жибек Нуржановна (Sarsenova Zhibek Nurzhanovna)

---

## 3. Project Scope and Current Status

### Scope

- **MVP** (first demo, end of December 2026): registration and login (IIN or phone, JWT), clinic and doctor catalog, nearest-slot search, booking / cancellation / rescheduling, referrals with statuses and expiry, referral-gated booking, doctor schedule, email reminders, administration of doctors and schedules.
- **v1** (pre-defense): family profiles, clinic map, waitlist, start / finish appointment, live queue, analytics dashboard, Kazakh and Russian interface.
- **v2** (if time permits): patient route, Telegram bot, automatic rescheduling when a doctor is ill, no-show prediction, QR check-in.
- **Out of scope:** payments, telemedicine, integration with real national systems; the demo uses synthetic data.

Interviews suggest moving the waitlist to MVP and automatic rescheduling to v1; this is to be agreed with the supervisor ([`docs/02-relevance.md`](docs/02-relevance.md#24-conclusion)).

### Current status (pre-defense 1, October 2026)

| Stage | Status |
|-------|--------|
| Proposal, evidence of relevance (6 interviews), literature review (21 sources) | Done |
| Existing system comparison (5 systems, hands-on tests), technology comparison | Done |
| Requirements with acceptance criteria | Done |
| Architecture and diagrams (context, use case, component, ER, sequence, states) | Done |
| Clickable prototype of key scenarios | Done |
| Verification and evaluation plan, milestones and risks | Done |
| Supervisor feedback | Requested on 23 Oct 2026, awaiting reply |
| Implementation | Not started — next milestone M2 (mid-November 2026) |

---

## 4. Pre-defense documentation

| # | Section | Document |
|---|---------|----------|
| 1 | Project proposal | [`docs/01-proposal.md`](docs/01-proposal.md) |
| 2 | Evidence of relevance | [`docs/02-relevance.md`](docs/02-relevance.md), protocols in [`docs/interviews/`](docs/interviews/) |
| 3 | Literature review | [`docs/03-literature.md`](docs/03-literature.md) |
| 4 | Existing system comparison | [`docs/04-existing-systems.md`](docs/04-existing-systems.md), screenshots in [`docs/screenshots/existing-systems/`](docs/screenshots/existing-systems/) |
| 5 | Technology comparison | [`docs/05-technologies-backend.md`](docs/05-technologies-backend.md), [`docs/05-technologies-frontend.md`](docs/05-technologies-frontend.md) |
| 6 | Requirements specification | [`docs/06-requirements.md`](docs/06-requirements.md) |
| 7 | Architecture and diagrams | [`docs/07-architecture.md`](docs/07-architecture.md), sources in [`diagrams/src/`](diagrams/src/), images in [`diagrams/png/`](diagrams/png/) |
| 8 | UX prototype | [`docs/08-prototype.md`](docs/08-prototype.md), prototype [`src/frontend/prototype/index.html`](src/frontend/prototype/index.html) |
| 9 | Verification and evaluation plan | [`docs/09-evaluation-plan.md`](docs/09-evaluation-plan.md) |
| 10 | Project management and risks | [`docs/10-management.md`](docs/10-management.md) |
| 11 | Supervisor feedback | [`docs/11-supervisor-feedback.md`](docs/11-supervisor-feedback.md) |

---

## 5. Selected Technologies

| Layer | Technologies |
|-------|--------------|
| Frontend | React, TypeScript, Vite, TanStack Query, Zustand, FullCalendar, Recharts, react-i18next |
| Maps | 2GIS API or Leaflet (decision in the frontend technology comparison) |
| Backend | Python, Django, Django REST Framework, simplejwt, drf-spectacular (Swagger) |
| Real time | Django Channels (WebSocket) |
| Background tasks | Celery + Celery Beat |
| Database | PostgreSQL (exclusion constraint against overlapping appointments) |
| Cache / broker | Redis (slot hold, cache, broker, channel layer) |
| ML | pandas, scikit-learn, XGBoost |
| Telegram bot | aiogram |
| Infrastructure | Docker Compose, GitHub, GitHub Actions, Sentry |
| Testing | pytest, Vitest, Playwright, Locust |

Justification: [`docs/05-technologies-backend.md`](docs/05-technologies-backend.md), [`docs/05-technologies-frontend.md`](docs/05-technologies-frontend.md).

---

## 6. Repository Structure

```
doctor-appointment-system/
├── src/
│   ├── backend/          # Django API, Celery, Channels (from M2)
│   ├── frontend/         # React app; prototype/ — clickable prototype
│   └── ml/               # no-show prediction (v2)
├── docs/                 # pre-defense sections 01–11
│   ├── interviews/       # interview guide and anonymized protocols
│   ├── screenshots/      # evidence for the existing system comparison
│   └── prototype/        # screenshots of the prototype
├── diagrams/
│   ├── src/              # editable diagram sources (Mermaid)
│   └── png/              # exported images
├── tests/
│   └── load/             # load tests (Locust)
├── .env.example          # configuration template with placeholder values
└── README.md
```

---

## 7. Links

- **Repository:** https://github.com/paranoia3/doctor-appointment-system
- **YouTrack project (DIP):** https://yerkegali.youtrack.cloud/projects/DIP/issues?sort=id
- **Prototype:** [`src/frontend/prototype/index.html`](src/frontend/prototype/index.html) — download and open in a browser; screenshots in [`docs/08-prototype.md`](docs/08-prototype.md)
- **API documentation (Swagger):** available after milestone M2

---

## 8. Development Workflow

- Every work item is a YouTrack task `DIP-N` with an assignee, priority, deadline and acceptance criteria.
- Work happens in a branch named after the task (`DIP-N-short-name`); commit messages start with `DIP-N:`.
- `main` is protected: changes only through pull requests with one approval from the other team member; force pushes and deletion are blocked.
- Each pre-defense submission is tagged: `pre-defense-1`, `pre-defense-2`, `final-pre-defense`.

---

## 9. Setup, Execution and Testing

> Runnable code is not available yet; implementation starts with milestone M2. This section will be updated with exact commands.

Planned workflow (one command for the whole project):

```bash
git clone https://github.com/paranoia3/doctor-appointment-system.git
cd doctor-appointment-system
cp .env.example .env        # fill in the required variables
docker compose up --build
```

Planned testing commands:

```bash
docker compose exec backend pytest --cov          # backend, coverage ≥ 70 %
cd src/frontend && npm run test                   # frontend unit tests (Vitest)
cd src/frontend && npx playwright test            # end-to-end tests
locust -f tests/load/locustfile.py                # load test: 100 concurrent bookings → exactly 1 succeeds
```
