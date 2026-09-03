# Week 9 — ML/AI design, and team building

**Goal:** Cover the round type that now appears in roughly half of loops. Finish the people stories.

**Due by Sunday**

- [ ] Recsys design
- [ ] RAG or LLM-serving design
- [ ] Story 13: growing someone; hiring-bar and process answers

| | | |
|---|---|---|
| [Master plan](../interview-prep-plan.md) | [Week index](README.md) | [Rubric](../staff-system-design.md#self-scoring-rubric-use-after-every-practice-problem) |

---

## Mon — Coding, 60 min · Mixed timed set

- [ ] 3 random mediums, 60 min total, no hints, no editorial until the end
- [ ] Log which pattern each one was and how long it took

*Done when:* You know your current medium-problem pace, honestly measured.

---

## Tue — Coding, 60 min · Concurrency & object design

- [ ] Thread-safe bounded blocking queue — state the invariants first
- [ ] Design a parking lot or elevator system: classes, interfaces, extension points
- [ ] Verbalize the class boundaries as you go; that's what is actually scored

*Done when:* You justify each class boundary by what it lets you change later.

---

## Wed — System design, 90 min

### Study, 30 min

- [ ] [ML System Design Interview Guide — Exponent](https://www.tryexponent.com/blog/machine-learning-system-design-interview-guide)
- [ ] [alirezadir MLSD template](https://github.com/alirezadir/machine-learning-interviews/blob/main/src/MLSD/ml-system-design.md)
- [ ] The retrieval → pre-rank → rank funnel and its per-stage latency budget
- [ ] Component cards: **feature store**, **vector DB**, **model serving tier**

### Timed problem, 45 min — no notes, on paper

**Recommendation / ranking system** (e.g. short-video feed) — 45 min. Then **RAG over enterprise documents**, covering permission-aware retrieval, freshness, eval, and cost per query — the four things most candidates miss.

### Staff move, 15 min · Capacity and cost for model serving

Sketch an LLM serving platform focused on capacity and cost: GPU batching, KV cache, model tiering, quota enforcement, and behavior when the upstream provider degrades.

### Team-lead lens (5 min, if lead roles are in scope)

Sequencing · team shape · risk to the date · support cost —
see [the four questions](../staff-system-design.md#using-this-plan-at-three-altitudes).

### Review

Score both designs with the rubric. ML designs usually score low on rows 5–6 the first time — check specifically for eval and cost.

*Done when:* you have a rubric score out of 24 and three named misses.

---

## Thu — Leadership & behavioral, 60 min · Team building

- [ ] Story 13 — **mentoring someone to a promotion / growing the team**
- [ ] Hiring bar and interview calibration: how you decide, and a time you were the dissenting no
- [ ] A broken process you fixed, with the before/after metric
- [ ] Staff framing for the same material: how you *scale yourself* — docs, defaults, review standards, things that outlived you

*Done when:* You can answer the same team question at lead altitude and at staff altitude.

---

## Fri — Review, 15 min

- [ ] Fill this week's row in the [progress tracker](../interview-prep-plan.md#part-6--progress-tracker)
- [ ] Name the one weakest thing this week
- [ ] Pick one fix to apply next week — one, not five

---

## Sat — Mock, 90 min

**ML/AI design mock**, or a second external mock on any Tier B/C problem.

---

## Week 9 gate

All 14 story slots are filled. Count them.

---

[← Week 8](week-08.md) · [Week index](README.md) · [Week 10 →](week-10.md)
