# 11. Supervisor Feedback

Version 1.0, 24 October 2026.

Supervisor: Сарсенова Жибек Нуржановна (Sarsenova Zhibek Nurzhanovna).

## 11.1 Review requests and meetings

| Date | Form | What was sent or discussed | Result |
|------|------|----------------------------|--------|
| 23.10.2026 | Message with links to the repository and YouTrack | All pre-defense materials (sections 1–10), with four questions: (1) is the scope and engineering complexity sufficient and which v2 features are mandatory; (2) can the no-show model be trained on the public Kaggle dataset while the system is demonstrated on synthetic data; (3) are text protocols of interviews sufficient; (4) one explanatory note for two students or separate parts, and its language | Awaiting reply |

## 11.2 Comments and decisions

Each comment is recorded with the decision taken and the commit or pull request where it was addressed.

| # | Comment | Decision | Commit / PR |
|---|---------|----------|-------------|
| — | No comments received yet | — | — |

## 11.3 Decisions that need the supervisor's confirmation

These proposals come from the interviews ([`02-relevance.md`](02-relevance.md#24-conclusion)) and will be applied after the supervisor agrees:

1. Move F7 "Waitlist" from v1 to MVP.
2. Move F18 "Automatic rescheduling when a doctor is ill" from v2 to v1.
3. Extend F15: the reminder 24 hours before the visit asks to confirm or cancel with one tap.
4. Add booking by registry staff on behalf of a patient in the same system.
5. Add a v2 requirement to limit active bookings for patients with repeated no-shows.

## 11.4 Next steps

- Record every comment in 11.2 as soon as it is received and address it in a separate pull request linked to a YouTrack task.
- Agree on regular checkpoints with the supervisor at each milestone (see [`10-management.md`](10-management.md#104-milestones)).
