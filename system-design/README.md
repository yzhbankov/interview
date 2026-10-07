# System Design — Working Material

| What | Where |
|------|-------|
| The 45-minute framework, estimation defaults, latency numbers | [`templates.md`](templates.md) |
| Component cards (Postgres, DynamoDB, decision table) | [`components/`](components/) |
| Worked problems — your notes (`structured.md`) and a reference solution (`ai-generated.md`) | folders below |
| The staff bar, rubric, problem catalog, delivery script | [`../staff-system-design.md`](../staff-system-design.md) |

Raw transcripts of the recorded solo sessions are in [`../archive/transcriptions/`](../archive/transcriptions/).

---

## Practice log

Every problem you have designed or watched, who drove it, and whether you have done it **as the
candidate, cold, at the staff bar**. A problem is only "done" when the last column is ✅.

Fill in the *Candidate* column — the plan needs to know which ones you only interviewed.

| Problem | When | Format | Candidate | Notes in repo | Solo, cold, staff-scored |
|---------|------|--------|-----------|---------------|--------------------------|
| URL shortener | Jan 2026 · Aug–Sep 2026 | solo recording · mock with friend | me · ? | [`url-shortener/`](url-shortener/) | ☐ |
| Rate limiter | Jan 2026 · Aug–Sep 2026 | solo recording · mock with friend | me · ? | [`rate-limiter/`](rate-limiter/) | ☐ |
| Web crawler | Feb 2026 | solo recording | me | [`web-crawler/`](web-crawler/) | ☐ |
| Dropbox / file sync | Feb 2026 | solo recording | me | [`dropbox/`](dropbox/) | ☐ |
| Ad click aggregator | Mar 2026 · Aug–Sep 2026 | solo recording · mock with friend | me · ? | [`ad-click-aggregator/`](ad-click-aggregator/) | ☐ |
| Distributed job scheduler | Aug–Sep 2026 | mock with friend | ? | — | ☐ |
| News feed | Aug–Sep 2026 | mock with friend | ? | — | ☐ |
| WhatsApp / chat | Aug–Sep 2026 | mock with friend | ? | — | ☐ |

**Rule for the ones your friend drove:** watching a design is not the same as producing one under
a clock. Week 1 (Wed and Sat) and Week 8 (Wed) in [`../weeks/`](../weeks/) are where you redo
them as the candidate.

**Reference breakdowns** for all eight: [Hello Interview problem breakdowns](https://www.hellointerview.com/learn/system-design/problem-breakdowns/)
(written) and the matching videos on [Hello Interview's YouTube channel](https://www.youtube.com/@hello_interview).
Read or watch only *after* your own attempt.

### Adding notes for a new problem

Create `system-design/<problem>/structured.md` with: requirements → numbers → API → data model →
high-level design → two deep dives → failure & blast radius → cost → phased plan → **rubric score
and three misses**. Add a `reference.md` or `ai-generated.md` only if you want a solution to diff against.
