# 11. Supervisor Feedback

Version 1.1, 24 October 2026.

Supervisor: Сарсенова Жибек Нуржановна (Sarsenova Zhibek Nurzhanovna).

## 11.1 Review requests and meetings

| Date | Form | What was sent or discussed | Result |
|------|------|----------------------------|--------|
| 23.10.2026 | Message with links to the repository and YouTrack | All pre-defense materials (sections 1–10), with four questions: (1) is the scope and engineering complexity sufficient and which v2 features are mandatory; (2) can the no-show model be trained on the public Kaggle dataset while the system is demonstrated on synthetic data; (3) are text protocols of interviews sufficient; (4) one explanatory note for two students or separate parts, and its language | Written review received on 24.10.2026, after the form was submitted (see 11.2) |

## 11.2 Review of 24.10.2026

Summary of the supervisor's written review:

- The project addresses a relevant problem for Kazakhstan; the problem is clearly defined.
- The scope is realistic: MVP, v1 and v2 stages with explicit exclusions.
- The engineering complexity is sufficient: multi-criteria slot selection, protection against double booking, referral lifecycle.
- Relevance is supported by interviews (3 medical workers, 3 patients), a literature review of 21 sources and hands-on tests of five existing systems; technology choices are justified.
- Requirements with acceptance criteria, architectural diagrams beyond the required minimum, a clickable prototype and a verification plan with a baseline are in place.
- Project management follows the required workflow (YouTrack, task branches, pull requests with mutual review, protected main); responsibilities are clearly allocated.
- Conclusion: the work fully meets the requirements of the first pre-defense; recommended grade “excellent”.

## 11.3 Comments and decisions

| # | Comment | Decision | Commit / PR |
|---|---------|----------|-------------|
| 1 | No corrective comments in the review of 24.10.2026 | No changes required; the review is recorded in this section | This pull request |
| 2 | Questions (1)–(4) from 23.10.2026 were not answered explicitly; the review confirms that the scope and complexity are sufficient (question 1, first part) | Questions on mandatory v2 features, the Kaggle dataset, interview protocols and the explanatory note were sent again for an explicit answer | — |

## 11.4 Decisions that need the supervisor's confirmation

These proposals come from the interviews ([`02-relevance.md`](02-relevance.md#24-conclusion)) and were sent to the supervisor together with the follow-up questions:

1. Move F7 "Waitlist" from v1 to MVP.
2. Move F18 "Automatic rescheduling when a doctor is ill" from v2 to v1.
3. Extend F15: the reminder 24 hours before the visit asks to confirm or cancel with one tap.
4. Add booking by registry staff on behalf of a patient in the same system.
5. Add a v2 requirement to limit active bookings for patients with repeated no-shows.

## 11.5 Next steps

- Record the answers to the follow-up questions in 11.3 and apply agreed changes in separate pull requests linked to YouTrack tasks.
- Agree on regular checkpoints with the supervisor at each milestone (see [`10-management.md`](10-management.md#104-milestones)).
