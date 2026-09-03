# Leadership & Behavioral Track — Senior / Team Lead / Staff

The behavioral track is where the level is actually decided. Coding and design rounds mostly
produce *hire / no-hire*; behavioral and deep-dive rounds produce *at what level*.

Companion to [`interview-prep-plan.md`](interview-prep-plan.md). Existing stories live in
[`star-stories/`](star-stories/).

---

## Part 0 — What is being scored

Interviewers are not scoring whether the story is impressive. They are scoring five dimensions,
and each has a level ladder:

| Dimension | Senior | Team Lead | Staff |
|-----------|--------|-----------|-------|
| **Scope** | one service, one team, one quarter | one team's delivery, multiple quarters | multiple teams or an org-wide concern, 1–2 years |
| **Ambiguity** | handled unclear requirements | turned a fuzzy goal into a plan with owners | *defined the problem itself*, including what not to do |
| **Influence** | convinced teammates with data | set direction for a team, handled dissent | changed behavior of people who don't report to you and had no reason to listen |
| **Impact** | shipped it, it worked | team velocity / quality / retention moved | a measurable business or org metric moved, and it stayed moved |
| **Judgment** | made a good call | made a good call under time pressure | made a good call under uncertainty, named the reversal condition, and was right about the second-order effects |

**The compression test.** Every story must survive being told in 2 minutes and then expanded to
6 minutes under follow-up questions. If it can't compress, it's unstructured. If it can't
expand, it's inflated.

### The three questions behind every behavioral question

1. *What did you actually do* (not the team — you)?
2. *Why did you do that instead of the obvious alternative?*
3. *How do you know it worked?*

If a story doesn't answer all three, it isn't finished. Question 2 is the one candidates skip and
the one that separates levels.

---

## Part 1 — Story portfolio (the 14 slots)

Build one story per slot. Some stories cover two slots; that's fine, but never reuse a story
twice in the same loop.

Legend: **S** = needed for senior, **L** = team lead, **F** = staff.

| # | Slot | Roles | Status in this repo |
|---|------|-------|---------------------|
| 1 | Production incident / on-call under pressure | S L F | ✅ [`session-1`](star-stories/session-1-jan-22.md#1-production-incident) — *needs staff-altitude rewrite (Week 8)* |
| 2 | Conflict with a peer | S L F | ✅ [`session-1`](star-stories/session-1-jan-22.md#2-conflict-with-teammate) — *needs cross-team version (Week 3)* |
| 3 | Disagree and commit | S L F | ✅ [`session-1`](star-stories/session-1-jan-22.md#3-technology-stack-decision) — strong; add what you'd do differently |
| 4 | Hiring & onboarding | L F | ✅ [`session-1`](star-stories/session-1-jan-22.md#4-hiring-and-onboarding) — extend with the hiring *bar* decision |
| 5 | Timeline slip / cutting scope | S L F | ✅ [`session-2`](star-stories/session-2-project-timeline.md) — good reflection already; add the number |
| 6 | Ambiguous requirements | S L F | ✅ [`session-3`](star-stories/session-3-vague-requirements.md) — strong staff signal, keep |
| 7 | **Influence without authority** | L F | ❌ **gap — Week 2** |
| 8 | **Driving alignment on a contentious decision across teams** | F | ❌ **gap — Week 2** |
| 9 | **I was wrong / changed my mind** | S L F | ❌ **gap — Week 3** |
| 10 | **Underperformance or difficult feedback** | L | ❌ **gap — Week 4** |
| 11 | **Saying no / managing a demanding stakeholder** | L F | ❌ **gap — Week 5** |
| 12 | **Technical strategy set over multiple quarters** | F | ❌ **gap — Week 6** |
| 13 | **Mentoring someone to a promotion / growing the team** | L F | ❌ **gap — Week 9** |
| 14 | **Decision made with incomplete or bad data** | F | ❌ **gap — Week 8** |

**Minimum viable portfolio (if you're short on time):** 1, 2, 3, 5, 6, 7, 9, 11.
Those eight cover roughly 80% of questions asked across all three role types.

### Story file template

Copy [`star-stories/TEMPLATE.md`](star-stories/TEMPLATE.md) for each new story. The two extra
fields beyond STAR are what make a story survive follow-ups:

```markdown
## N. <Slot name>

**Situation:** 2 sentences. Company, team size, timeframe, why it mattered commercially.
**Task:** 1–2 sentences. Your specific ownership. "I", not "we".
**Action:** 4–5 sentences. Concrete steps. At least one decision with a named alternative.
**Result:** 2–3 sentences. A number. Then the durable change.

**Alternative I rejected:** what else was on the table, and the condition under which
I'd have chosen it instead.
**What I'd do differently:** one honest thing. Not a humblebrag.
**Scope label:** senior / lead / staff — which of the three bars this demonstrates.
```

---

## Part 2 — Question bank by round

### Round A — Peer / values behavioral (all roles)

- Tell me about a time you disagreed with someone more senior than you.
- Tell me about the hardest technical problem you've solved. Why was it hard?
- Tell me about a time you made a mistake that had real consequences.
- Tell me about a time you had to deliver with incomplete information.
- Tell me about feedback you received that changed how you work.
- What's something you believe about engineering that most of your colleagues disagree with?

### Round B — People & delivery (team lead / TLM — the round most under-prepared)

- How do you run a 1:1? What do you do when someone has nothing to talk about?
- Walk me through how you'd handle an engineer whose output has dropped for two months.
- You have 6 engineers and a roadmap that needs 9. What do you do?
- How do you decide who gets the interesting work?
- A senior engineer on your team is technically excellent and consistently rude in code review. Go.
- Your PM commits to a date in front of the VP without asking you. What happens next?
- How do you know your team is healthy? Which specific signals do you watch?
- Tell me about someone you hired who didn't work out. What did you learn about your own bar?
- How much do you code as a lead, and how do you decide when to?
- How do you protect the team from thrash without becoming a blocker?

**The trap in this round:** answering as an individual contributor who happens to help people.
Lead answers have *mechanisms* in them — a cadence, a document, a rubric, a plan with dates —
not just good intentions.

### Round C — Influence & scope (staff)

- Tell me about something you changed that outlived your involvement.
- How did you get a team that doesn't report to you to change their roadmap?
- Describe a technical direction you set. How did you know it was right? How was it measured?
- Tell me about a time you deliberately chose the less elegant solution.
- What's a project you killed, or argued should not be done?
- How do you decide what *not* to work on?
- Where does your organization's architecture currently have the most risk, and what would you do about it?
- Tell me about a time you were the only person who saw a problem.

**The trap in this round:** describing a big project you were *on* rather than a decision you
*owned*. Staff interviewers listen for the moment the story turns on something you personally
decided or wrote.

### Round D — Hiring manager (all roles, sets your level)

- Walk me through your career. Why each move?
- What are you looking for that you don't have today?
- What would your last manager say your biggest weakness is?
- What kind of work makes you lose track of time?
- Why this company, this team, this role?
- What level do you think you're at, and why?

That last one gets asked more often than people expect. Have a two-sentence answer that names
scope, not years: *"I've been operating at [scope] — owning [X] across [N teams/quarters] — which
maps to your [level]. I'd want the loop to test that rather than take my word for it."*

---

## Part 3 — The project deep dive (45 minutes, staff-critical)

The format: you pick one project, describe it, and the interviewer interrupts continuously.
It is the highest-fidelity signal in the whole loop, because it's the hardest round to fake.

### Choosing the project

Pick the one where **all** of these are true:

- You made the important decisions, not just the implementation.
- There was real ambiguity at the start.
- Something went wrong.
- You can state the outcome in a number.
- You can draw the architecture from memory in 3 minutes.

Prefer a project that is 1–3 years old and finished over a shiny in-flight one. You need to know
how it turned out, including the parts that aged badly.

### The 45-minute structure

| Minutes | What you cover | The signal it produces |
|---------|----------------|------------------------|
| 0–5 | Business context and why the project existed. Who wanted it, what it was worth. | Do you understand *why*, or only *what*? |
| 5–10 | Your specific role, team shape, timeline, constraints. | Scope and honesty |
| 10–20 | Architecture. Draw it. Data flow, key components, scale numbers. | Technical depth |
| 20–32 | **Three decisions**, each with the alternative you rejected and why. | Judgment — the core of the round |
| 32–38 | What went wrong, and what it cost. | Self-awareness, and whether you learn |
| 38–43 | Outcome with a metric; what changed durably after you left it. | Impact |
| 43–45 | What you'd do differently with today's knowledge. | Growth |

### Preparation checklist

- [ ] One-page written brief covering all seven rows above.
- [ ] Architecture diagram you can redraw from memory, twice, without notes.
- [ ] Three decisions, each with: the alternative, the deciding factor, the reversal condition.
- [ ] Every number you cite verified — QPS, data volume, latency, team size, cost, timeline.
      **Never state a number you can't defend two questions deep.**
- [ ] Answers ready for the five predictable attacks:
      *"Why not just use X?"* · *"What would break at 10×?"* ·
      *"What was the biggest risk you took?"* · *"What would you cut if you had half the time?"* ·
      *"Who disagreed with you, and what did they say?"*

---

## Part 4 — Drills

### Drill 1 — The 2-minute compression

Take any story. Tell it in 2 minutes, timed, out loud. Then cut it to 90 seconds. Then expand it
to 5 minutes by adding only technical detail — not more context. If expanding forces you to add
*context*, your opening was too thin.

*Cadence: every Thursday, one story.*

### Drill 2 — The scoping ladder

Take one story and tell it three times: as a senior answer, as a lead answer, as a staff answer
(see [`interview-prep-plan.md`](interview-prep-plan.md) Part 1). The facts don't change — the
emphasis, the "I", and the closing sentence do. This is the highest-leverage drill in this file,
because it converts your existing stories into three levels of ammunition instead of one.

### Drill 3 — The "so what?" ladder

State your result. Then ask "so what?" three times.

> *"Test coverage went from 40% to 80%."* → so what? →
> *"Post-release bugs dropped by half."* → so what? →
> *"QA stopped being the release bottleneck; we went from monthly to weekly releases."* → so what? →
> *"Features reached customers 3 weeks sooner, and the team stopped working weekends before releases."*

That fourth line is the one to say in the interview. Most candidates stop at the first.

### Drill 4 — The follow-up gauntlet

Have someone (or a timer and a list) hit every story with: *Why you and not someone else? ·
What would have happened if you'd done nothing? · Who disagreed? · What did it cost? ·
How do you know it worked? · What would you do differently?*

A story that survives all six is done. Most need two rounds of rewriting to get there.

### Drill 5 — Weakness rehearsal

Write your real weakness, out loud, in the form: *what it is · a concrete instance where it cost
something · the specific mechanism I now use · evidence the mechanism works.* Fake weaknesses
("I care too much") are transparent and cost real points at lead and staff level.

---

## Part 5 — Openers

Prepare three versions of "tell me about yourself" — 60–90 seconds each. Use the one matching the
level on the req.

**Senior:**
> "I'm a senior engineer with [N] years, mostly in [domain]. At [company] I own [system], which
> does [scale number]. Most recently I [specific technical achievement with a metric]. I'm looking
> for [what], because [honest reason]."

**Team lead:**
> "I'm an engineer who's been leading [team size] for [duration] while staying hands-on. Beyond
> delivery, I've built [process/onboarding/hiring artifact] — for example, [the onboarding
> framework story, in one sentence, with the 'productive in two weeks' number]. I'm looking for a
> role where I own both the technical direction and the team's growth."

**Staff:**
> "I work at the boundary between architecture and delivery. At [company] I was system architect
> and project owner for [cross-cutting system spanning hardware, firmware, cloud] — which meant
> defining the problem before designing it, as with [the QA platform story in one clause]. What
> I'm looking for is scope where the hard part is deciding *what* to build."

Note how the staff version leads with a *way of working* and the senior version leads with a
*system*. That difference is the whole point.

---

## Part 6 — Questions to ask them

Three per interviewer type. Questions are scored — vague questions read as low interest.

**For the hiring manager**
- What does the first successful 6 months look like, concretely?
- What's the hardest unsolved problem on this team right now?
- How is this role's level calibrated, and what would push someone from this level to the next here?

**For a peer engineer**
- What's the thing about the codebase or architecture that a new person is most surprised by?
- How do technical decisions actually get made here — who writes what down?
- What's the last thing the team disagreed about, and how did it end?

**For a skip-level / staff interviewer**
- Where does the current architecture make you nervous over the next two years?
- What work is important but nobody currently owns?
- How does staff-level work get recognized here versus tech-lead work?

**Never ask** anything answerable from the careers page, and never ask about compensation in a
technical round.

---

## Part 7 — Anti-patterns that cost levels

| Anti-pattern | Why it costs | Fix |
|--------------|--------------|-----|
| "We" throughout | Interviewer can't score you | Say "I" for your actions, "the team" only for context |
| No numbers | Reads as unmeasured work | Every story ends with one metric — and a defensible one |
| Story runs 6 minutes unprompted | Reads as poor communication | 2-minute default; let them pull for detail |
| Inflating scope | Collapses under follow-ups, and they always follow up | Tell the true scope, at the highest honest altitude |
| Only success stories | Reads as low self-awareness | Two of your stories must contain real failure |
| Blaming others | Fatal at lead and staff | Own the part you controlled; describe others neutrally |
| Process with no outcome | "We adopted Scrum" — so what? | Run Drill 3 on it |
| Answering a people question as an IC | Misses the level entirely | Name a mechanism: a cadence, a doc, a rubric, a plan |
| No stated alternative | Reads as luck rather than judgment | Every technical story names the road not taken |
