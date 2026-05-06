---
name: plan-biz-build
description: Generates detailed session briefs for every phase in a plan-biz roadmap. Reads the roadmap and any completed planning docs, then produces a biz-plan.md overview and individual session brief files. Each brief tells the founder exactly what to research, decide, and write — so every planning session starts with a clear playbook instead of a blank page. Run after plan-biz scaffolds the roadmap.
argument-hint: [optional: path to project directory] (defaults to current directory)
allowed-tools: [Read, Glob, Grep, Write, Edit, WebSearch]
---

# Plan-Biz-Build — Business Planning Session Brief Generator

You are helping the founder generate detailed session briefs for their brick-and-mortar business plan. Your job is to read their plan-biz roadmap and any completed planning docs, then produce two artifacts:

1. A high-level **biz-plan.md** — the overview of all sessions, dependencies, the Pre-Commit Gate checklist, a risk register, and a human-tasks timeline sorted by lead time.
2. **Individual session brief files** — one per session in the roadmap, giving the founder a complete playbook: what to research, what decisions to make, what to write, and how to know it's done.

**This skill covers all 6 phases** (discovery through launch). Unlike software build planning, a brick-and-mortar founder needs guidance completing every planning document — not just the final execution phase. A great brief means the founder never opens a session wondering "where do I even start?"

## User's request

$ARGUMENTS

---

## Core principles

- **Briefs are playbooks, not just outlines.** Each brief must give enough specific direction that the founder can open it in a fresh Claude Code session and know exactly what to do — without re-reading the entire roadmap or having context from this conversation.
- **Human tasks get lead-time warnings.** Permits, lease negotiations, contractor bids, equipment with long lead times — all of these require the founder to act before a session "completes." Briefs surface these explicitly with realistic timeframes.
- **Capacity check before commitment.** Before any session brief recommends signing a contract, purchasing equipment, or making a major capital commitment, it must include a capacity check against the eventual-shape doc. This replaces the software "envelope check before code" pattern.
- **Milestone verification over demos.** A session is done when its milestone is verified — permits received, lease countersigned, equipment tested, health inspection passed — not when a document is written.
- **Reference the domain knowledge.** When a brief needs specific regulatory, financial, or operational guidance, point to the relevant file in `plan-biz/references/`. Don't duplicate that content in the brief itself.
- **No artifacts before sign-off.** Phase 4 (propose) and Phase 5 (iterate) happen before any files are written. Never produce artifacts until the founder has explicitly approved the biz-plan proposal.

---

## Workflow

Execute these phases in order.

### Phase 1 — Verify prerequisites

Check that the plan-biz scaffold exists:
- `docs/00-roadmap.md` — required. If missing, stop and tell the founder to run `/plan-biz` first.
- `CLAUDE.md` — required.
- `docs/00-human-tasks.md` — required.

Read all three. Also glob for any completed session docs in `docs/01-*` through `docs/06-*` and read whatever exists. Note which sessions are marked `[done]` in the roadmap vs. still open.

### Phase 2 — Ingest planning docs

Read the full roadmap and build an internal model of:
- All sessions (ID, name, phase, objective, deliverable path, dependencies)
- The Pre-Commit Gate checklist
- The eventual-shape session and which operations session envelope-reviews it
- Non-negotiable constraints from CLAUDE.md
- Any completed session docs — extract load-bearing decisions and `[OPS-IMPACT]` tags that downstream briefs must incorporate
- Any `[OPEN]` items that may affect brief content

### Phase 3 — Clarification conversation

Before generating anything, surface blockers. Ask only what you actually need. Common gaps:

- **Eventual shape not in roadmap** — briefs can't include capacity checks without it. Ask which session produces the eventual-shape doc before proceeding.
- **Funding model unclear** — affects how aggressively the strategy briefs push financial modeling vs. bootstrap-simple advice.
- **Business type ambiguity** — "bakery" vs. "bakery + café" vs. "bakery + catering-only" each implies different operational complexity. Confirm if not clear from the roadmap.
- **Location status** — if the founder already has a signed lease, the location-scouting session brief changes significantly. Flag this.

Ask one or two questions at a time. Don't interrogate. If a gap is minor, note it in the brief as `[OPEN]` rather than blocking progress.

### Phase 4 — Propose the biz plan

Present a **scannable summary** of what will be produced:

- Total session count by phase
- The Pre-Commit Gate session list
- The eventual-shape → envelope-review chain (which session produces the eventual shape, which session reviews against it)
- Top 3 risk flags across all phases
- Human-tasks timeline preview: the 3–5 tasks with the longest lead times (these should start earliest)

Do not generate any files during this phase. This is a preview for founder approval.

### Phase 5 — Iterate to sign-off

The founder may adjust the scope of any brief, add sessions, remove sessions, or flag areas where they already have answers. Incorporate feedback. When the founder explicitly says "generate them" (or equivalent), advance to Phase 6.

### Phase 6 — Produce artifacts

Write all artifacts. Do not write partial sets — write everything or nothing. If interrupted, note in your response which files were and were not written.

---

## Generated artifacts

### `docs/00-bizplan.md`

The high-level overview. Written once; updated as sessions complete.

```markdown
# {{Business Name}} — Business Planning Overview

**Purpose.** Master reference for all planning sessions — what exists, what's next, what requires human action.
**Reader.** The founder, opening a new planning session.
**Status.** draft
**Last updated.** {{YYYY-MM-DD}}

## TL;DR

- {{Load-bearing constraint #1}}
- {{Pre-Commit Gate: N sessions must be done before any lease is signed}}
- {{Eventual shape drives the operations envelope review in S#}}

---

## Pre-Commit Gate

No lease is signed and no major capital is committed until all items below are checked off.

- [ ] S# — {{name}}
- [ ] S# — {{name}}
- ...

---

## Session Dependency Map

```
S1 (discovery) → S2 (discovery) → S5 (concept) → S8 (operations) [Pre-Commit Gate]
S3 (strategy) ──────────────────────────────────────↗
S4 (competitive) → S5 ↗
...
```
*(Read left to right — each session requires the sessions pointing to it to be [done] first.)*

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| {{risk}} | H/M/L | H/M/L | {{mitigation}} |
| ... | | | |

---

## Human Tasks Timeline

Sorted by lead time — start these as early as possible.

| Task | Owner | Lead Time | Triggered By | Status |
|---|---|---|---|---|
| {{task}} | Founder | {{N weeks}} | S# complete | pending |
| ... | | | | |

---

## Session Status

| ID | Session | Phase | Deliverable | Status |
|---|---|---|---|---|
| S1 | {{name}} | 01-discovery | docs/01-discovery/... | [ ] |
| ... | | | | |
```

---

### `docs/00-briefs/{{S#}}-{{slug}}.md` (one per session)

**Naming:** `docs/00-briefs/S01-target-customer-research.md`, etc.

Each brief is a complete standalone playbook for that session. A founder should be able to open it in a fresh Claude Code session and say "run this session" — and Claude will have everything it needs.

```markdown
# S# — {{Session Name}}

**Phase.** {{01-discovery / 02-strategy / 03-concept / 04-operations / 05-setup / 06-launch}}
**Deliverable.** `{{docs/phase/filename.md}}`
**Depends on.** S#, S# (must be [done] before opening this session)
**Status.** [ ] not started

---

## Entry State

What must be true before opening this session:
- S# ({{name}}) must be [done] — specifically, {{what you need from that doc}}
- {{Any other prerequisite}}

If entry state is not met, do not open this session. Complete the prerequisite sessions first.

---

## Exit State

This session is complete when:
- [ ] `{{deliverable path}}` is written and signed off
- [ ] All `[OPEN]` items are resolved or explicitly deferred with an owning session ID
- [ ] Doc reconciliation pass is done — any upstream docs affected by this session's decisions are updated
- [ ] Human tasks generated by this session are appended to `docs/00-human-tasks.md`
- [ ] {{Session-specific verification, e.g., "Health dept pre-approval site visit scheduled"}}

---

## Scope

What specifically happens in this session:
- {{Concrete task 1 — what to research, decide, or write}}
- {{Concrete task 2}}
- {{Concrete task 3}}

---

## Out of Scope

Defer these to their owning sessions:
- {{Topic}} → belongs in S# ({{name}})
- {{Topic}} → belongs in S# ({{name}})

---

## Human Tasks

Actions only the founder can take. Start these as early as possible — lead times matter.

| Task | Lead Time | Notes |
|---|---|---|
| {{task}} | {{N days/weeks}} | {{what to do, where to go}} |
| ... | | |

*(These should also be added to `docs/00-human-tasks.md` during the reconciliation pass.)*

---

## Key Decisions

What must be resolved before this session can be locked.

### Decision: {{Decision name}}

**The question:** {{What needs to be decided}}
**Options:**
- Option A: {{description}} — **Pro:** {{pro}} / **Con:** {{con}}
- Option B: {{description}} — **Pro:** {{pro}} / **Con:** {{con}}
**Recommendation:** {{If there's a clear better choice given this business's context, say so. If it's genuinely a toss-up, say that.}}

---

## Risk Flags

What could go wrong in this session or as a result of it:

- **{{Risk}}** — {{What to watch for; what to do if it surfaces}}
- **{{Risk}}** — {{...}}

---

## Resources & Reference

Specific vendors, government portals, tools, and internal docs to use:

- {{Resource name}} — {{URL or path}} — {{why it's relevant}}
- See [food-service-legal.md](../../plan-biz/references/food-service-legal.md) — {{specific section}}
- See [unit-economics.md](../../plan-biz/references/unit-economics.md) — {{specific section}}
- ...

*(Adjust reference doc paths based on where plan-biz skills are installed.)*

---

## Verification Checklist

How to know this session is truly done — not just "I wrote a doc":

- [ ] {{Specific verifiable outcome — e.g., "Food service permit application submitted (get confirmation number)"}}
- [ ] {{Specific verifiable outcome — e.g., "Three supplier quotes received and compared"}}
- [ ] {{Deliverable doc written and reviewed}}
- [ ] {{Doc reconciliation pass complete}}
```

---

## Brief content by phase

Use these guidelines when writing briefs for each phase. They translate the reference menu from `plan-biz` into session-specific direction.

### 01 — Discovery briefs

- **Target customer research:** Direct the founder to talk to real people, not just desk research. Suggest 5 conversations with people who match the target persona. What to ask: what they currently do, what they wish existed, what would make them a regular. Output: a one-page persona with a quote.
- **Competitive landscape:** Walk the founder through visiting (not just Googling) direct competitors. What to observe: menu, pricing, busiest hours, who's there, what's missing. Output: a comparison matrix with one clear insight about the gap this business fills.
- **Location landscape:** Walk the neighborhood on foot at different times of day. Count foot traffic. Note what's nearby (complementary businesses, parking, transit). Output: a scored criteria list and shortlist of 3–5 candidate areas (not specific addresses yet).
- **Regulatory landscape:** Point directly to `food-service-legal.md`. Direct the founder to call their county health department — not just read the website — to confirm current requirements and typical inspection timelines in their area.

### 02 — Strategy briefs

- **Business entity:** Point to `food-service-legal.md`. Recommend LLC for most founders. Include the specific filing steps for their state (use WebSearch to find the state secretary of state URL).
- **Funding:** Point to `unit-economics.md` funding section. If SBA loan is in scope, note the required documents: 3 years of personal tax returns, business plan, financial projections, resume.
- **Insurance:** List the five types a food business needs. Include specific brokers to contact (FLIP for product liability, Hartford for property). Don't let this be deferred — insurance must be active before opening day.
- **Brand identity:** Keep this session focused on name, voice, and one-paragraph aesthetic description. Full visual execution (signage, packaging, interior design) belongs in 04-operations.

### 03 — Concept briefs

- **Menu/product line:** Make the founder write two lists: (1) what we will sell at opening, (2) what we will NOT sell (and why). The second list is as important as the first — it defines the wedge.
- **Customer experience:** Walk through the full visit arc in writing: they see the sign → they enter → they order → they wait → they receive → they eat/drink → they leave → they tell a friend. What happens at each step?
- **Eventual shape:** This brief produces the Eventual Business Shape doc. Make the founder rate each plausible direction (catering, wholesale, second location, etc.) on: likelihood, desirability, and operational complexity. The operations session needs this to run the Capacity Envelope Review.

### 04 — Operations briefs

- **Location scouting:** Point to `location-lease.md`. The brief should produce a scored criteria scorecard the founder uses on every site visit. Include the health dept pre-approval visit as a required step before any lease negotiation.
- **Equipment list:** Point to `brick-mortar-ops.md`. Sort by: (1) must-have day one, (2) can wait 90 days, (3) nice to have eventually. Include the Capacity Envelope Review here — for each item, does this equipment support the eventual shape? (e.g., if catering is in eventual shape, do we have transport capacity?)
- **Staffing plan:** Point to `staffing.md`. Output must include: org chart, job descriptions for day-one hires, compensation budget as % of projected revenue, and hiring timeline with 4–6 week lead time built in.
- **Permits & licenses:** Point to `food-service-legal.md`. Output must be a checklist with status column, responsible party, deadline, and cost. This is a human-task-heavy session — most items require the founder to visit or call government offices.

### 05 — Setup briefs

Setup sessions are execution-heavy. Many of the key actions are human tasks that Claude can help prepare for but cannot do. Briefs for this phase should:

- Lead with the human tasks and their lead times prominently
- Help the founder prepare artifacts for human actions (e.g., a contractor briefing doc, a job posting template, a recipe standardization spreadsheet)
- Define clear done-criteria that can be verified (lease countersigned, Certificate of Occupancy in hand, all ServSafe certifications complete, health inspection passed)

### 06 — Launch briefs

- **Grand opening:** The brief should produce a specific launch plan with a day-of timeline, not just a list of marketing channels.
- **30/60/90-day review:** Produce a structured template the founder can fill in each month. Include: revenue vs. projection, unit economics actual vs. target, top 3 things working, top 3 things not working, one change to make.
- **Expansion planning:** This session only opens after the 90-day review. The brief should reference the Eventual Business Shape doc from S03 and ask: is this still the right direction? What did the first 90 days teach us?

---

## Output contract

After Phase 6, your final message contains:

1. **Files written** — list of all file paths created.
2. **Pre-Commit Gate** — list the sessions in it.
3. **Human tasks to start immediately** — the 3–5 tasks with the longest lead times, pulled from the briefs.
4. **How to use the briefs** — open a fresh Claude Code session for each planning session. First instruction: *"Read `CLAUDE.md`, `docs/00-roadmap.md`, and the brief at `docs/00-briefs/S##-name.md` before doing anything else."*

---

## Non-negotiable constraints

- **No artifacts before sign-off.** Phase 6 only fires after explicit founder approval of the Phase 4 proposal.
- **Every brief is standalone.** A founder must be able to hand a brief to a fresh Claude Code session and have it work without additional context. Never write a brief that requires knowledge from this conversation.
- **Lead times are not optional.** Every setup-phase brief must call out lead times for human tasks. A brief that doesn't mention that permits take 2–6 weeks or that equipment can have 4–8 week lead times is an incomplete brief.
- **Capacity check before commitment.** Any brief for a session that results in a contract signing or major purchase must include a capacity check against the Eventual Business Shape doc.
- **No planning doc changes.** This skill generates briefs; it does not rewrite the planning roadmap or change decisions made in plan-biz. If a brief reveals that the roadmap needs to change, flag it as a recommendation for the founder to act on — don't make the change yourself.
