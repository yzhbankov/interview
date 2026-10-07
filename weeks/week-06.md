# Week 6 — The project deep dive, and AI system design II

**Week of:** Mon 16 Nov 2026
**Goal:** The staff-critical week. The deep dive is where staff offers are won and lost and almost nobody practices it. Coding this week switches to the two new round formats.

**Due by Sunday**

- [ ] The full deep-dive package for one project
- [ ] LLM inference API design
- [ ] One AI-assisted coding session with a written bug list

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · AI-assisted coding round

Follow [`coding-track.md` § AI-assisted coding round](../coding-track.md#ai-assisted-coding-round).

- [ ] Pick a ~2-hour-by-hand task (e.g. CSV log ingester reporting top-k errors per minute, with tests)
- [ ] 45 min with Cursor / Claude Code / Copilot. **Narrate every prompt and every acceptance decision aloud**
- [ ] 15 min: written list of every bug, missed edge case or bad design choice the tool produced, and what you did

*Done when:* the bug list is non-empty and you ran at least one test you wrote yourself.

## Tue — Coding, 60 min · Code comprehension

- [ ] Take ~500 lines of an open-source module you don't know
- [ ] 25 min: explain its data flow aloud, from entry point to side effects
- [ ] 20 min: find one real issue (race, unhandled error path, off-by-one) and fix it
- [ ] 15 min: how would you extend it with one feature? Which boundary would you change?

*Done when:* you read for 10 minutes before touching anything.

---

## Wed — System design, 90 min · LLM inference API

**Study, 20 min**

- [ ] Continuous batching, KV cache, token streaming, model tiering — the vocabulary, one line each
- [ ] [techinterview.org — AI in system design interviews, 2026](https://www.techinterview.org/post/3233474919/ai-in-system-design-interviews-2026/) for what interviewers now probe

**Timed, 45 min — no notes**

**A multi-tenant LLM inference API** for an internal platform: streaming responses, per-team quotas and billing, routing across model tiers, behavior when the upstream provider degrades. This is 2026's URL shortener — expect it.

**Staff move, 15 min — capacity and cost**

Cost per million tokens: the dominant line item (GPU hours) and the two levers that halve it (batching, caching/tiering) with the latency each costs. Then multi-tenant isolation: how one team's batch job doesn't starve another's chat.

**Score, 10 min** — rubric, three misses. Then pick the project for Thursday.

*Done when:* score, misses, and a cost-per-million-tokens figure.

---

## Thu — Behavioral, 90 min · The project deep dive — take the extra 30 minutes

- [ ] Read [`leadership-behavioral.md` § Part 3](../leadership-behavioral.md#part-3--the-project-deep-dive-45-minutes-staff-critical)
- [ ] Choose the project against the five criteria: you decided, it was ambiguous, something went wrong, there's a number, you can draw it. The hardware/firmware/cloud system from the [timeline story](../star-stories/session-2-project-timeline.md) or the [QA platform](../star-stories/session-3-vague-requirements.md) are the obvious candidates
- [ ] One-page brief covering all seven rows of the 45-minute structure
- [ ] Draw the architecture from memory. Twice. No notes
- [ ] Three decisions, each with: the alternative, the deciding factor, the reversal condition
- [ ] Verify every number you plan to cite. Never state one you can't defend two questions deep

*Done when:* you can run the whole 45 minutes without notes.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] One weakest thing; one fix

---

## Sat — Mock, 90 min · Deep dive, with your friend

Hand your friend the five attack questions from Part 3 and ask them to interrupt with *"why not X?"* every five minutes. That is the real format of the round.

---

## Week 6 gate

The deep dive survived hostile interruption without you losing the thread or inventing a number.

---

[← Week 5](week-05.md) · [Week index](README.md) · [Week 7 →](week-07.md)
