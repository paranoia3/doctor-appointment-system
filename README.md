# Online Doctor Appointment System with Electronic Referrals and Nearest-Slot Selection

A web system where a patient books a doctor's appointment in 1–2 minutes: the system automatically finds the nearest suitable free slot and manages electronic referrals from doctor to doctor.

> **Official topic:** Development of an online doctor appointment system with electronic referrals and selection of the nearest free time.

---

## Table of Contents

1. [Problem Statement, Target Users, Expected Outcomes](#1-problem-statement-target-users-expected-outcomes)
2. [Team and Supervisor](#2-team-and-supervisor)
3. [Project Scope and Current Status](#3-project-scope-and-current-status)
4. [Selected Technologies](#4-selected-technologies)
5. [Links](#5-links)
6. [Setup, Execution and Testing](#6-setup-execution-and-testing)

---

## 1. Problem Statement, Target Users, Expected Outcomes

### Problem statement

Existing appointment systems in Kazakhstan (Damumed, eGov, private clinic portals) make patients browse doctors one by one and manually look for free time. They rarely take the distance to the clinic into account, handle referrals between specialists poorly, and do nothing to reduce no-shows. As a result patients lose time, and clinics lose capacity to missed appointments.

### Target users

| Role | Description |
|------|-------------|
| **Patient** | Any registered user; can also book for family members (child, parent) |
| **Doctor** | Clinic staff: views the schedule, starts/finishes appointments, issues referrals |
| **Clinic administrator** | Registry staff or manager: manages doctors, rooms, schedules, sees analytics |
| **Super admin** | System developers: manage clinics, specialties, service types and all users |

### Expected outcomes

- Booking in 1–2 minutes with automatic selection of the 5 best slots (by waiting time, distance and time-of-day convenience).
- Guaranteed protection against double booking (Redis hold + transactional row lock + PostgreSQL exclusion constraint).
- Electronic referrals with a full lifecycle: *Issued → Used → Closed*, or *Expired / Cancelled*; narrow specialists are bookable only with an active referral.
- Measurable improvement over a naive "earliest slot" approach (average waiting time and distance), evaluated experimentally.
- Reduced no-shows through ML-based risk prediction and extra reminders.

---

## 2. Team and Supervisor

| Member | Role | Responsibilities |
|--------|------|------------------|
| Yerkegali Yergali | Backend developer | DB models and migrations, exclusion constraint, authentication and roles, slot generation and nearest-slot algorithm, booking/cancel/reschedule/hold, referrals and statuses, patient route, WebSocket queue, Celery tasks, ML no-show model, Telegram bot, Django admin, backend and load tests |
| Aigerim Askarova | Frontend developer | Figma mockups, registration/login/profiles, doctor search, filters and clinic map, slot selection and booking calendar, patient and doctor cabinets, live queue screen, admin analytics dashboard, KZ/RU localization, responsive layout, API mocks (MSW), E2E tests |
| **Both** | — | Analysis of existing systems, API contract (Swagger), repository / Docker Compose / Git rules, test data (5 clinics, 30 doctors, 200 patients), demo scenarios |

**Supervisor:** Сарсенова Жибек Нуржановна (Sarsenova Zhibek Nurzhanovna)

---

## 3. Project Scope and Current Status

### Scope

Features are split into three priority levels:

- **MVP** — minimum working version for the first demo to the supervisor
  - Registration/login by IIN or phone (JWT), password recovery
  - Clinic and doctor catalog with filters (specialty, clinic, language)
  - Nearest free slot search by specialty
  - Booking, cancellation and rescheduling with confirmation
  - Referrals: issuing, "narrow specialist only by active referral", statuses and automatic expiry
  - Doctor cabinet: daily/weekly schedule, patient card with visit history
  - Email reminders (24 h and 2 h before)
  - Administration: doctors, rooms, schedules, vacations
- **v1** — full version for the pre-defense
  - Family profiles, clinic map with distance sorting, waitlist
  - "Start/finish appointment" buttons, live queue (WebSocket)
  - Analytics dashboard, Kazakh and Russian interface
- **v2** — extras for the final defense (if time permits)
  - Patient route ("test → result → specialist"), Telegram bot
  - Automatic rescheduling when a doctor is ill, ML no-show prediction, QR check-in

### Key algorithms

1. **Nearest slot selection** — score = `w1·T + w2·D + w3·P` (waiting time, distance, inconvenient-time penalty; normalized 0–1; initial weights 0.6 / 0.3 / 0.1, tuned experimentally).
2. **Double-booking protection** — 5-minute Redis hold, transaction with `SELECT … FOR UPDATE`, PostgreSQL exclusion constraint.
3. **Referral lifecycle** — automatic status transitions by events and schedule (Celery).
4. **Patient route** — chained steps respecting result-readiness time, same-clinic/same-day merging.
5. **No-show prediction** — logistic regression, Random Forest, XGBoost compared by ROC-AUC, Recall, F1.

### Non-functional targets

- Nearest-slot search ≤ 1 s (100 doctors, 14-day horizon); 95% of API requests ≤ 500 ms for up to 200 concurrent users
- HTTPS, password hashing, JWT (access 15 min / refresh 7 days), role-based access, audit log of medical data access, IIN masking, compliance with the Law of the Republic of Kazakhstan on personal data
- Responsive layout from 360 px; latest Chrome, Safari, Firefox, Edge
- Backend test coverage ≥ 70%; one-command startup via Docker Compose

### Current development status

| Stage | Status |
|-------|--------|
| Technical specification | Draft, v0.1 (29 Sep 2026) |
| Analysis of existing systems | Planned |
| Architecture, DB and API design | Planned |
| Implementation | Not started |
| Target | Working MVP by the end of December |


---

## 4. Selected Technologies

| Layer | Technologies |
|-------|--------------|
| Frontend | React, TypeScript, Vite, Ant Design or shadcn/ui, TanStack Query, Zustand, FullCalendar, Recharts, react-i18next |
| Maps | 2GIS API or Leaflet |
| Backend | Python, Django, Django REST Framework, simplejwt, drf-spectacular (Swagger) |
| Real time | Django Channels (WebSocket) |
| Background tasks | Celery + Celery Beat |
| Database | PostgreSQL |
| Cache / broker | Redis |
| ML | pandas, scikit-learn, XGBoost (Kaggle "Medical Appointment No Shows" dataset for experiments) |
| Telegram bot | aiogram |
| Infrastructure | Docker Compose, GitHub, GitHub Actions, Sentry |
| Testing | pytest (backend), Vitest and Playwright (frontend), Locust (load) |

The web app and the Telegram bot share one REST API (`/api/v1/`); the live queue uses WebSocket; Celery runs background jobs from the same Django codebase via Redis.

---

## 5. Links

- **YouTrack project:** _<add link>_
- **Project documentation / technical specification:** _<add link>_
- **API documentation (Swagger):** _<add link once available>_
- **Design mockups (Figma):** _<add link>_

---

## 6. Setup, Execution and Testing

> Runnable code is not available yet. This section will be completed as soon as the first version of the backend and frontend is committed.

Planned workflow (target, one command for the whole project):

```bash
git clone <repository-url>
cd <repository-folder>
cp .env.example .env     
docker compose up --build
```

Planned testing commands:

```bash
# Backend
docker compose exec backend pytest --cov

# Frontend
cd frontend && npm run test        # Vitest
cd frontend && npx playwright test # E2E

# Load test (double booking: 100 concurrent requests, exactly 1 must succeed)
locust -f loadtests/locustfile.py
```

_Exact commands, ports and environment variables will be updated soon_
