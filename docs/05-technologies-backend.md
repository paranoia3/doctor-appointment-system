# 5. Technology Comparison — Backend

Author: Yerkegali Yergali · Version 1.0, 21 October 2026
Frontend decisions are described in [`05-technologies-frontend.md`](05-technologies-frontend.md).

## Method

Each decision is made against the project requirements, not against general popularity. For every key decision we list 3 realistic alternatives, compare them on the criteria that matter for this project, and state how the choice will be checked in practice.

Ratings: **++** fully meets the criterion · **+** meets with extra work · **–** does not meet or needs a workaround.

Requirements referred to below (from the technical specification):

| ID | Requirement |
|----|-------------|
| F4 | Nearest free slot search across all doctors of a specialty |
| F5 | Booking, cancellation and rescheduling |
| F7 | Waitlist: a freed slot is offered to the next patient |
| F10 | Referral statuses and automatic expiry |
| F14 | Live queue updated in real time |
| F15 | Reminders 24 h and 2 h before the visit |
| F16 | Telegram bot using the same API |
| F17 | Administration of doctors, rooms, schedules, vacations |
| F20 | ML no-show prediction |
| NFR-P | Nearest-slot search ≤ 1 s; 95 % of API requests ≤ 500 ms at 200 concurrent users |
| NFR-D | Two appointments of one doctor must never overlap (double booking is impossible) |

Summary of decisions:

| # | Decision | Chosen | Alternatives considered |
|---|----------|--------|-------------------------|
| 1 | Backend framework | Django + Django REST Framework | FastAPI, NestJS |
| 2 | Database | PostgreSQL | MySQL, MongoDB |
| 3 | Background and scheduled tasks | Celery + Celery Beat | RQ, cron + management commands |
| 4 | Real-time transport for the live queue | WebSocket (Django Channels) | Server-Sent Events, polling |
| 5 | Temporary slot hold (5 minutes) | Redis key with expiry | Hold table in PostgreSQL, in-process memory |

---

## Decision 1. Backend framework

| Criterion | Why it matters | Django + DRF | FastAPI | NestJS (Node.js) |
|-----------|----------------|:---:|:---:|:---:|
| Admin interface for clinics and schedules | F17 must be usable early, without building a separate UI | **++** built-in Django admin | **–** no admin; separate tool or own UI | **–** no admin |
| Database constraints and migrations | NFR-D relies on a PostgreSQL exclusion constraint | **++** ORM supports `ExclusionConstraint`, migrations built in | **+** SQLAlchemy + Alembic, configured manually | **+** TypeORM / Prisma, exclusion constraint via raw SQL |
| Authentication and roles | 4 roles, JWT for web and Telegram bot (F16) | **++** users, groups, permissions built in; JWT via simplejwt | **+** assembled from libraries | **+** Passport.js modules |
| Same language as the ML module | F20 must live in the same codebase | **++** Python | **++** Python | **–** TypeScript; ML would need a separate Python service |
| WebSocket support | F14 | **+** Django Channels (ASGI) | **++** native | **++** gateways |
| API documentation | Contract between frontend and backend | **++** drf-spectacular (OpenAPI) | **++** automatic OpenAPI | **++** Swagger module |
| Raw performance | NFR-P | **+** synchronous by default, ORM overhead | **++** asynchronous, high throughput | **++** asynchronous |
| Development speed for a 2-person team | Fixed deadline | **++** "batteries included" | **+** more code for admin and auth | **+** more code; second language in the stack |

**Choice: Django + DRF.** The deciding requirements are the admin panel (F17), database-level protection against double booking (NFR-D) and keeping the ML module in one Python codebase (F20). FastAPI is faster in raw throughput, but our performance target (500 ms at 200 users) is modest, so development speed and built-in features matter more. NestJS would split the project into two languages.

**Risk and check.** Synchronous Django may be slower under load. This is checked with a Locust test against NFR-P; if needed, the slot search endpoint is optimized (caching of generated slots, query tuning) before changing the framework.

Sources: [Django admin](https://docs.djangoproject.com/en/stable/ref/contrib/admin/), [Django PostgreSQL constraints](https://docs.djangoproject.com/en/stable/ref/contrib/postgres/constraints/), [FastAPI](https://fastapi.tiangolo.com/), [NestJS](https://docs.nestjs.com/); framework benchmarks — see [`03-literature.md`](03-literature.md).

---

## Decision 2. Database

| Criterion | Why it matters | PostgreSQL | MySQL | MongoDB |
|-----------|----------------|:---:|:---:|:---:|
| Forbid overlapping appointments in the database | NFR-D | **++** exclusion constraint on a time range per doctor | **–** no exclusion constraints; overlap checked in application code | **–** no such constraint; checked in application code |
| Transactions and row locking | Booking under concurrent requests (F5) | **++** ACID, `SELECT … FOR UPDATE` | **++** ACID (InnoDB), `SELECT … FOR UPDATE` | **+** multi-document transactions since 4.0, require a replica set |
| Relational data model | User → profile → appointment → doctor → clinic → referral | **++** | **++** | **–** document model; joins via `$lookup` |
| Time ranges and dates | Schedules, vacations, slots | **++** range types (`tstzrange`) | **+** separate start/end columns | **+** separate fields |
| Support in Django | Decision 1 | **++** extra features in `django.contrib.postgres` | **++** | **–** not supported by the Django ORM |

**Choice: PostgreSQL.** It is the only option that enforces "no overlapping appointments" in the database itself. With MySQL or MongoDB this guarantee would depend on application code only, which is exactly the place where race conditions happen.

**Risk and check.** The constraint needs the `btree_gist` extension; it is enabled in a migration. The guarantee is checked by a load test: 100 concurrent requests for one slot must produce exactly one appointment.

Sources: [PostgreSQL exclusion constraints](https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-EXCLUSION), [MongoDB transactions](https://www.mongodb.com/docs/manual/core/transactions/); database benchmarks — see [`03-literature.md`](03-literature.md).

---

## Decision 3. Background and scheduled tasks

The system has two kinds of background work: **event-driven** (send a confirmation after booking, offer a freed slot to the waitlist — F7) and **scheduled** (reminders 24 h and 2 h before — F15, expiry of referrals — F10, nightly re-training or scoring for F20).

| Criterion | Celery + Celery Beat | RQ | cron + Django management commands |
|-----------|:---:|:---:|:---:|
| Event-driven tasks from request code | **++** | **++** | **–** no queue |
| Scheduled (periodic) tasks | **++** Celery Beat | **+** needs an extra scheduler package | **++** but only periodic |
| Retries on failure (e.g. email server down) | **++** built in | **+** basic | **–** must be written manually |
| Broker | Redis or RabbitMQ (we already use Redis) | Redis only | none |
| Complexity | **+** more configuration | **++** very simple | **++** simplest |
| Monitoring | **++** Flower | **+** RQ dashboard | **–** logs only |

**Choice: Celery + Celery Beat.** It covers both event-driven and scheduled work with one tool and has retries, which matter for reminders. RQ is simpler but would need a second package for periodic tasks; cron cannot react to events such as a cancelled appointment.

**Risk and check.** Celery has more settings to get right. Each task is covered by a test, and scheduled tasks are checked with a shortened schedule in the test environment.

Sources: [Celery periodic tasks](https://docs.celeryq.dev/en/stable/userguide/periodic-tasks.html), [RQ](https://python-rq.org/).

---

## Decision 4. Real-time transport for the live queue (F14)

| Criterion | WebSocket (Django Channels) | Server-Sent Events | Polling every N seconds |
|-----------|:---:|:---:|:---:|
| Update delay | **++** immediate | **++** immediate | **–** up to N seconds |
| Server load with many open screens | **++** one connection, messages only on change | **++** one connection | **–** a request every N seconds from every screen |
| Two-way communication | **++** | **–** server → client only | **+** separate requests |
| Reconnection | **+** handled in client code | **++** automatic in the browser | **++** not needed |
| Integration with Django | **+** Channels, ASGI server, Redis channel layer | **+** streaming responses, long-lived connections | **++** ordinary endpoint |

**Choice: WebSocket via Django Channels.** For the read-only queue screen alone, SSE would be enough. WebSocket is chosen because the queue also receives events from the doctor's actions ("start / finish appointment") and from QR check-in in v2, and Redis, which Channels needs as a channel layer, is already in the stack.

**Risk and check.** If WebSocket is blocked by a network, the client falls back to polling the queue endpoint. Update delay and the number of open connections are checked during load testing.

Sources: [Django Channels](https://channels.readthedocs.io/), [MDN: Server-Sent Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events).

---

## Decision 5. Temporary slot hold

While a patient confirms a booking, the chosen slot is held for 5 minutes so that nobody else takes it. This is the first of three levels of double-booking protection (NFR-D).

| Criterion | Redis key with expiry | Hold table in PostgreSQL | In-process memory |
|-----------|:---:|:---:|:---:|
| Atomic "take if free" | **++** `SET key value NX EX 300` | **+** unique constraint + insert | **–** not shared between server processes |
| Automatic expiry after 5 minutes | **++** built in | **–** needs a cleanup job | **+** |
| Load on the main database | **++** none | **–** extra writes for every hold | **++** none |
| Works with several backend instances | **++** | **++** | **–** |

**Choice: Redis.** It provides an atomic hold with automatic expiry in one command and keeps short-lived data out of the main database. Redis is already used for Celery and Channels, so no new component is added. Even if a hold is lost, the database constraint from Decision 2 still prevents double booking.

Sources: [Redis SET command](https://redis.io/docs/latest/commands/set/).

---

## Other backend choices (no separate comparison)

| Area | Choice | Reason |
|------|--------|--------|
| Authentication | JWT (access 15 min, refresh 7 days) via simplejwt | The same API serves the web app and the Telegram bot; no server-side sessions needed |
| API documentation | drf-spectacular (OpenAPI / Swagger) | Generated from code, so the frontend works from an always up-to-date contract |
| Error monitoring | Sentry | Required by the reliability requirement; free tier is enough |
| Load testing | Locust | Python, scenarios written as code, used for NFR-P and NFR-D |
| ML libraries | pandas, scikit-learn, XGBoost | The three models (logistic regression, Random Forest, XGBoost) are compared experimentally; see the evaluation plan |
