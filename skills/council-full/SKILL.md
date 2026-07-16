---
name: council-full
description: Convene the full 9-seat product council (Product, UX, Engineering, Data, QA, Red-Team, Customer Voice, Business/GTM, + a Chair) for high-stakes decisions: case studies, 0-1 bets, positioning, monetization, go/no-go. Seats answer independently and blind, a Chair synthesizes and applies a readiness gate, the user decides. Use for big multi-path calls; for a quick ambiguous call a single second-opinion pass is enough.
---

# council-full: convene the 9-seat product council

Full governance, roster, model guidance, and rationale: **`~/.claude/council/CHARTER.md`** (installed from `agents/council/CHARTER.md` in this repo). Read it if you have not. This skill is the runbook.

## What this is
A discipline panel that pressure-tests a high-stakes decision and returns ONE honest CEO brief. Advisory only: the user decides. The edge comes from seats answering independently and blind, then a Chair surfacing the disagreement instead of averaging it away.

## Vocabulary (used by every seat and the gate)
- **Verdict families:** APPROVE, APPROVE-WITH-CONDITIONS, REVISE, BLOCK. Each seat opens its answer with `VERDICT: <one of these> | CONFIDENCE: <0-100>`.
- **Confidence 0-100** = the seat's self-reported, uncalibrated rating of how likely its verdict is right given the brief, not how strongly it feels — a coarse ordinal signal (higher = more sure), not a measured probability.

## When to use it
High-stakes, multi-path decisions. NOT for trivial tasks, plain code review, or one-right-answer questions. For a quick single ambiguous call, don't convene the council; a plain second-opinion pass is enough.

## Run it (from the MAIN thread; the orchestrator fans out subagents, so a subagent cannot run this)

### 1. Frame the question + build ONE neutralized brief
Reduce it to a single explicit prompt: what are we deciding, what constraints bind, what counts as success, and the required context (for any product/customer decision, name the real buyer/segment). Neutralize the brief: strip leading conclusions, state it as an open question. Every seat gets this same brief as its task input (note: Claude Code subagents still inherit the repo's CLAUDE.md/memory, so a seat is not fully sandboxed from project context — keep that context neutral if you need true blindness). If it is vague, ask the user ONE clarifying question first.

### 2. Pick the seats (don't reflexively fire all 9)
- Mid-stakes single deliverable (fast path) -> Product + Red-Team + the one most-relevant domain seat + Chair.
- Product / strategy bet -> Product, UX, Data, Red-Team, Customer Voice, Business/GTM.
- Technical / architecture -> Engineering, Data, QA, Red-Team (+ Product if scope is in play).
- Go/no-go / ship -> Product, QA, Red-Team, Business/GTM.
- Full high-stakes (audition-grade, big bet) -> all 8 discipline seats.
If the decision's risk surface is not covered, add the relevant seat. Summon a witness only if a seat needs depth it does not own; a witness returns evidence and analysis that informs the seats, never a voting verdict, and is never counted in convergence. Prefer one whose frontmatter `tools:` list is genuinely read-only (e.g. an architecture or security reviewer) — the "must not Write/Edit/Bash" instruction is a convention, not an enforced sandbox, so pick a witness whose granted tools actually withhold mutation rather than trusting the prompt.

### 3. Fan out the discipline seats: PARALLEL, INDEPENDENT, BLIND
Dispatch each chosen seat as a subagent **in a single batch** (parallel) via the Agent tool, `subagent_type` = the slug: `council-product`, `council-ux`, `council-engineering`, `council-data`, `council-qa`, `council-redteam`, `council-customer`, `council-gtm`. Pass the shared brief as the seat's task input (subagents still inherit repo CLAUDE.md/memory, so keep project context neutral for true blindness). Never pass one seat another seat's output. Red-Team and Customer Voice MUST be blind. If you have pinned models per seat in frontmatter, do not also set a model at dispatch.

A seat that returns nothing is an ABSTENTION, EXCEPT: if a dispatched BLOCKING seat fails to return a parseable verdict, the council is GATED (re-dispatch that seat or escalate) and must not be reported CLEAR. Blocking seats are Red-Team (in every panel) plus Data and Customer Voice when the panel includes them; a seat you did not select does not gate.

### 4. Synthesize via the Chair
Randomize the order of the collected verdicts (reduces position bias — it can't remove framing or shared-model correlation), then dispatch `council-chair` with the original brief PLUS the randomized verdicts. The Chair surfaces dissent verbatim, never averages, applies the gate, represents abstentions as Verdict=ABSTAIN excluded from the X/N denominator, and returns the CEO brief.

### 5. Apply the readiness gate
GATED if: an unresolved refusal/safety flag; an unresolved high-conviction BLOCK; two high-conviction seats in opposite verdict families (confidence is a band, not a >= cutoff — it is uncalibrated); an unresolved settled-but-unproven Red-Team premise; or a dispatched blocking seat returned nothing. Otherwise CLEARS. Never gate on a low average; unanimity is a warning sign, not a pass. If the result is suspiciously unanimous or clustered, before reporting CLEARS, re-run the single most-pivotal seat on a sharpened or neutralized brief (or apply a stated discount), per the charter's correlation response. If GATED on a single genuine fork, the orchestrator (you) runs at most ONE targeted, anonymized second round on just that fork; if it still does not resolve, escalate to the user. No open debate rounds.

### 6. Hand the user the brief
Present the CEO brief. The user decides. If the project has a docs folder, offer to save it to `docs/council-reviews/<date>-<slug>.md`; otherwise present inline.

## Cost discipline
A full council is ~10-15x a single pass. Size the panel to the stakes (use the fast path for mid-stakes). Fan out concurrently so each wave's wall-clock is its slowest seat; budget for up to three serial waves (fan-out, optional second round, synthesis). Calibration stays OFF unless this is a long-lived project (CHARTER).

## Anti-patterns
- Letting seats see each other's answers (kills the whole point).
- Showing the Red-Team or Customer Voice the consensus before they answer.
- Averaging conflicting verdicts into a soft middle (the Chair must not).
- Reporting CLEAR when a dispatched blocking seat returned nothing (fail-safe: that GATES).
- Firing all 8 on a small call. Correlated seats give far fewer effective votes than their headcount.
- Running this from a subagent (it cannot fan out), or setting a model at dispatch when your seats carry frontmatter pins.
