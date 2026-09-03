# Week 8 — Multi-region, failure, cost, and failure stories

**Goal:** Overload, cascading failure, and the honest failure stories that carry the most signal.

**Due by Sunday**

- [ ] Retry-storm redesign + postmortem-style doc
- [ ] Multi-region version of your chat design
- [ ] Incident story at staff altitude; story 14

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Tries & design problems

- [ ] [Implement Trie](https://leetcode.com/problems/implement-trie-prefix-tree/)
- [ ] [Word Search II](https://leetcode.com/problems/word-search-ii/)
- [ ] [LRU Cache](https://leetcode.com/problems/lru-cache/) — HashMap + doubly linked list

*Done when:* You can build LRU in under 15 minutes, clean.

---

## Tue — Coding, 60 min · Practical / API-shaped coding — increasingly what lead and staff loops ask

- [ ] An in-process rate limiter (token bucket) with a clean public interface
- [ ] Retry with exponential backoff **and jitter**, cancellable
- [ ] A bounded LRU with TTL and eviction callbacks
- [ ] For each: name the interface first, write tests, no LeetCode tricks

*Done when:* Someone else could use your API from the signature alone.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] [Google SRE Book](https://sre.google/books/) Ch. 21 (handling overload), Ch. 22 (cascading failures)
- [ ] [Builders' Library — Timeouts, retries, backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter/)
- [ ] [Builders' Library — Load shedding to avoid overload](https://aws.amazon.com/builders-library/using-load-shedding-to-avoid-overload/)
- [ ] Active-active vs active-passive, RTO/RPO, data residency, cell-based architecture
- [ ] Component cards: **multi-region topology**, **circuit breaker / load shedder**

### Timed problem, 45 min — no notes, on paper

Two Tier C problems this week. (1) *"We had a 4-hour outage from a retry storm. Redesign so it can't recur."* (2) *"Make this multi-region"* — using your Week 6 chat design as the input.

### Staff move, 15 min · Postmortem as design doc

Write the retry-storm answer as a postmortem-style doc: contributing factors, the design changes, and the detection that would have caught it in 5 minutes instead of 4 hours.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

Compare your detection story against how you actually found the incident in your real production-incident story. The gap between them is your Thursday material.

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · Failure track

- [ ] Rewrite [the production incident story](../star-stories/session-1-jan-22.md#1-production-incident) at staff altitude: not "I fixed it" but **what you changed structurally so the class of failure ended, and what it cost**
- [ ] Add a **postmortem culture** answer — blameless review, action-item follow-through
- [ ] Story 14 — **a decision made with incomplete or bad data**
- [ ] [Drill 5](../leadership-behavioral.md#drill-5--weakness-rehearsal): rehearse your real weakness, with the mechanism and the evidence it works

*Done when:* Two of your stories now contain genuine failure that you own.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**Behavioral + hiring-manager mock**, 45 min — including "what level do you think you're at?" and "what are your questions for me?"

---

## Week 8 gate

You have a two-sentence level answer that names scope, not years.

---

[← Week 7](week-07.md) · [Week index](README.md) · [Week 9 →](week-09.md)
