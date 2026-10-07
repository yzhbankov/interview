# Week 5 — AI system design I: RAG

**Week of:** Mon 9 Nov 2026
**Goal:** The design round that now appears in roughly half of senior+ loops. Plus the strategy story and the honest failure story.

**Due by Sunday**

- [ ] RAG design with eval and cost per query
- [ ] Story 12; incident story rewritten at staff altitude

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Tries & LRU

- [ ] [Implement Trie](https://leetcode.com/problems/implement-trie-prefix-tree/)
- [ ] [LRU Cache](https://leetcode.com/problems/lru-cache/) — HashMap + doubly linked list; target under 15 min
- [ ] Stretch: [Word Search II](https://leetcode.com/problems/word-search-ii/)

*Done when:* LRU in under 15 minutes, clean.

## Tue — Coding, 60 min · Practical, API-shaped

From [`coding-track.md` § Practical problems](../coding-track.md#practical--api-shaped-problems-lead-and-staff-loops). Interface first, tests, no tricks.

- [ ] Token-bucket rate limiter with a public interface
- [ ] Retry with exponential backoff **and jitter**, cancellable
- [ ] If time: bounded LRU with TTL and eviction callbacks

*Done when:* someone could use each API from the signature alone.

---

## Wed — System design, 90 min · RAG over enterprise documents

**Study, 20 min**

- [ ] Chip Huyen, *AI Engineering* — the RAG chapter (chunking, hybrid retrieval, reranking, eval)
- [ ] [Design Gurus — AI/ML system design roadmap 2026](https://www.designgurus.io/blog/prepare-for-ai-ml-system-design-interview-2026) for the shape of the round

**Timed, 45 min — no notes**

**An internal assistant that answers questions over a company's documents** (wikis, tickets, drive). Must cover the four things most candidates miss: **permission-aware retrieval** · **freshness** (a doc edited 5 minutes ago) · **evaluation** (how you know answers got worse) · **cost per query**.

**Staff move, 15 min — failure and eval**

What does a confidently wrong answer cost, and how would you detect a quality regression within a day of a model or index change? Name the offline eval set, the online signal, and the kill switch (fall back to plain search).

**Score, 10 min** — rubric. AI designs score low on rows 5–6 the first time; check for eval and cost specifically.

*Done when:* score, misses, and a cost-per-query figure you can defend.

---

## Thu — Behavioral, 60 min · Strategy and failure

- [ ] **Story 12 — a multi-quarter technical direction you set**: the doc you wrote, who adopted it, how adoption was measured. If you have no org-scale version, write the honest team-scale one; inflated scope collapses on the second follow-up
- [ ] Rewrite the [production incident](../star-stories/session-1-jan-22.md#1-production-incident) at staff altitude: not "I fixed it" but *what you changed structurally so the class of failure ended, and what it cost*. Use the gap you found in Week 3 Wed
- [ ] Add a **postmortem culture** answer: blameless review, action-item follow-through

*Done when:* story 12 survives "who disagreed with this direction, and what did they say?"

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] One weakest thing; one fix

---

## Sat — Mock, 90 min · Coding, one hard

1 hard problem, 35 min, aloud. Then 15 min written: where did the time go?

---

## Week 5 gate

You can explain how a RAG system fails and what it costs per query without hand-waving either.

---

[← Week 4](week-04.md) · [Week index](README.md) · [Week 6 →](week-06.md)
