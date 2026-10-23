# 10. Project Management and Risks

Version 1.0, 23 October 2026.

## 10.1 Tools and links

| Tool | Purpose | Link |
|------|---------|------|
| GitHub | Source code, documentation, diagrams, reviews | https://github.com/paranoia3/doctor-appointment-system |
| YouTrack (project `DIP`) | Tasks, assignees, priorities, deadlines, board | https://yerkegali.youtrack.cloud/projects/DIP/issues?sort=id |
| Figma / prototype | Mockups of key scenarios | https://github.com/paranoia3/doctor-appointment-system/blob/main/src/frontend/prototype/index.html |

## 10.2 Responsibilities

| Member | Role | Main areas |
|--------|------|------------|
| Yerkegali Yergali | Backend developer | Data model, API, slot algorithm, booking and double-booking protection, referrals, background jobs, live queue server side, ML no-show model, load tests |
| Aigerim Askarova | Frontend developer | Mockups, patient / doctor / administrator interfaces, map, live queue screen, analytics dashboard, KZ/RU localization, E2E tests |
| Both | — | Analysis of existing systems, interviews, API contract, test data, demo scenarios, report |
| Supervisor: Сарсенова Жибек Нуржановна | Supervisor | Reviews key deliverables and decisions at each checkpoint |

## 10.3 Workflow

- Every work item is a YouTrack task `DIP-N` with an assignee, priority, deadline and acceptance criteria.
- Work is done in a branch named after the task (`DIP-N-short-name`); commit messages start with the task ID (`DIP-N: …`).
- Changes reach `main` only through a pull request. The other team member reviews it; `main` is protected (pull request and 1 approval required, no force pushes, no deletion).
- The YouTrack task gets a link to its pull request and is closed only when its acceptance criteria are met.
- Each pre-defense submission is marked with a Git tag (`pre-defense-1`, `pre-defense-2`, `final-pre-defense`).

## 10.4 Milestones

Exact dates of the pre-defenses are taken from the university schedule and will be updated when announced.

| # | Milestone | Planned | Result |
|---|-----------|---------|--------|
| M1 | Analysis and design package (this submission) | 24 Oct 2026 | Proposal, evidence, literature, comparisons, requirements, architecture, prototype, evaluation plan; tag `pre-defense-1` |
| M2 | Environment and data model | mid-November 2026 | Docker Compose, database schema with exclusion constraint, authentication, Swagger contract, seed data |
| M3 | Core booking | early December 2026 | Slot generation and ranking, booking with hold, cancellation, frontend search and booking screens |
| M4 | MVP | end of December 2026 | All MVP requirements, email reminders, referrals, doctor schedule, administration; first demo to the supervisor |
| M5 | Version v1 | February 2027 | Family profiles, map, waitlist, live queue, analytics, KZ/RU; experiments E1–E3; tag `pre-defense-2` |
| M6 | Version v2 and experiments | March–April 2027 | Selected v2 features (patient route, no-show model), experiments E5–E7 |
| M7 | Final pre-defense and defense | per university schedule | Final report, demo, tag `final-pre-defense` |

## 10.5 Risk register

Probability and impact: **H** — high, **M** — medium, **L** — low.

| # | Risk | P | I | Mitigation | Owner |
|---|------|---|---|------------|-------|
| R1 | Study load (exams, other courses) reduces available time — this happened in the week of 20–22 October | H | M | Small tasks with clear deadlines in YouTrack; weekly check of the board; v2 features can be dropped without affecting the defense | Both |
| R2 | Scope is too large for two people | M | H | Strict priorities MVP → v1 → v2; MVP by the end of December; v2 only if MVP and v1 are done | Both |
| R3 | One team member is unavailable for a period | M | H | Shared API contract (Swagger), API mocks on the frontend (MSW), documented setup in README so the other member can continue | Both |
| R4 | Concurrency bugs lead to double booking | M | H | Three protection levels; concurrent load test E2 in CI before each milestone | Yerkegali |
| R5 | Performance targets are not met | M | M | Early load test (M3); caching of generated slots; query optimization | Yerkegali |
| R6 | Not enough real users for interviews and usability sessions | M | M | Recruit through family, friends and university; phone interviews of 10–15 minutes; report the sample size honestly | Aigerim |
| R7 | No access to real clinic data | H | M | Synthetic data generator with published parameters; real systems are out of scope | Yerkegali |
| R8 | Map API limits or terms (2GIS) | M | L | Leaflet with OpenStreetMap as a fallback; distances computed on the backend from coordinates | Aigerim |
| R9 | No-show model trained on foreign data does not transfer | H | L | Model is v2; logistic regression baseline; limitation stated in the report | Yerkegali |
| R10 | Personal data requirements (Law of RK on personal data) | L | H | Only synthetic data; IIN masking; no real recordings or names in the repository | Both |
| R11 | Supervisor feedback arrives late | M | M | Meetings planned at each milestone; written comments via GitHub pull requests | Both |

## 10.6 Dependencies

| Dependency | Needed for | Status |
|------------|------------|--------|
| Interviews with patients and medical staff | Section 2, requirement priorities | In progress |
| Supervisor review | Section 11, scope decisions | In progress |
| Map service (2GIS API key or Leaflet + OpenStreetMap) | F6 | To be chosen in M3 |
| SMTP account for emails | F15 | Needed by M4 |
| Telegram bot token | F16 (v2) | Needed in M6 |
| Kaggle dataset "Medical Appointment No Shows" | F20, E7 | Publicly available |

## 10.7 Next steps after this submission

1. Address the supervisor's comments and update the documents (section 11).
2. Finalize the API contract in Swagger and the database schema (M2).
3. Set up Docker Compose and continuous integration with tests on every pull request.
4. Start the core booking module and the search and booking screens (M3).
