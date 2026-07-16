# The Council: Charter

A personal, project-agnostic product council: a panel of discipline seats that pressure-tests a high-stakes decision and hands the user one honest brief. It works in any project or chat.

> Convene it with the **`council-full`** skill (ships in this repo at `skills/council-full/SKILL.md`), or ask Claude to "convene the council on X". For a fast, low-stakes ambiguous call, do not convene the full panel — use the fast-path panel under seat selection, or a plain second-opinion pass.

---

## When to convene it

Use it for decisions where being wrong is expensive and more than one path is credible: audition-grade work (interview case studies, take-homes, submissions, pitches), 0-1 product bets, segment / positioning / moat / monetization calls, go/no-go with real downside.

Do NOT convene it for trivial or obvious tasks, plain code review (use language-specific reviewers), or a question with one right answer. A council that fires on everything becomes a tax, not an edge.

---

## Governance

**Advisory only. The user is the sole decision-maker.** Every seat returns a recommendation with a confidence and a rationale. No seat has a veto. No seat overrules another. The Chair synthesizes; it does not decide. The user decides.

---

## The roster (9 seats)

| Seat | subagent_type | Brings |
|---|---|---|
| Product | `council-product` | validated value + viability (Cagan's four risks), outcome over output |
| UX/UI | `council-ux` | Nielsen heuristics, error/empty states, accessibility, reads-as-a-real-product |
| Engineering & Feasibility | `council-engineering` | can-we-build-it, riskiest technical assumption first, build-vs-buy, scale |
| Data & Analytics | `council-data` | denominators, base rates, significance, leading + guardrail metrics |
| QA & Risk | `council-qa` | operational failure modes (FMEA), pre-mortem, blast-radius containment |
| Red-Team & Skeptic | `council-redteam` | attacks the premise; load-bearing premise + pre-mortem. **Blind.** |
| Customer / User Voice | `council-customer` | the real buyer (JTBD); refuses invented quotes/stats. **Blind.** |
| Business / GTM / Monetization | `council-gtm` | bottom-up sizing, CAC/LTV, willingness-to-pay, moat beyond cost |
| Chair & Synthesizer | `council-chair` | merges verdicts, surfaces dissent, applies the gate, writes the CEO brief. Does NOT vote. |

The Chair is the 9th seat but does not cast a verdict; the council has **8 voting seats + the Chair**. "All 9" and "all 8 discipline seats + Chair" mean the same panel.

---

## The protocol (lean core)

1. **State the question** + constraints + what success looks like + **required context** (for any product or customer decision, name the real buyer/segment; if it is missing, that is the first thing to fix). Assemble ONE shared brief and **neutralize it**: strip leading conclusions, present the decision as an open question. Run the council from the **main thread** (it fans out subagents; a subagent cannot fan out).
2. **Pick the seats** (see seat-selection). Don't reflexively fire all 9. If the decision's risk surface is not covered by the default set, add the relevant seat.
3. **Fan out the chosen discipline seats as parallel, independent subagents**, each given only the shared brief. If you pin models per seat in frontmatter (see model assignment below), take no dispatch-time model action. Red-Team and Customer Voice are strictly blind. A non-responding seat is an abstention, EXCEPT a **blocking seat that was dispatched but returned nothing** forces a GATE. Blocking seats are Red-Team (in every panel) plus Data and Customer Voice when the panel includes them; a seat deliberately not selected does not gate.
4. **Randomize the verdict order**, then pass the **brief plus the verdicts** to the Chair (`council-chair`). The Chair surfaces dissent and applies the gate.
5. **Apply the gate.** If GATED on a single genuine fork, the **orchestrator** (not the Chair) runs at most ONE targeted, anonymized second round on just that fork. If still unresolved, escalate to the user with the fork stated plainly. No open debate rounds, no further loops.
6. **The user decides.** Optionally save a review artifact (below).

### Seat selection by decision type
- **Quick gut-check / one ambiguous call** -> don't convene the council; a single second-opinion pass is enough.
- **Mid-stakes, single deliverable (fast path)** -> Product + Red-Team + the one most-relevant domain seat + Chair (4 dispatches). The bridge between a plain second opinion and the full panel.
- **Product / strategy bet** -> Product, UX, Data, Red-Team, Customer Voice, Business/GTM + Chair.
- **Technical / architecture** -> Engineering, Data, QA, Red-Team + Chair (+ Product if scope is in question).
- **Go/no-go / ship** -> Product, QA, Red-Team, Business/GTM + Chair.
- **Full high-stakes (audition, large bet)** -> all 8 discipline seats + Chair.

---

## Anti-anchoring (the spine)

- Seats answer **independently and blind**. Red-Team and Customer Voice are strictly blind: a skeptic who sees the consensus mirrors it; a customer voice that fills gaps fabricates.
- Give every seat the **same neutralized brief** so independence varies judgment, not facts.
- **Decorrelate the seats (the #1 lever).** A panel of correlated voting seats yields far fewer effective votes than its headcount. Primary decorrelation is the sharply distinct mandate + refusal spine each seat carries, plus the blind-independent protocol.
- **Correlation response:** if verdicts come back suspiciously unanimous or tightly clustered, the Chair states the correlation risk in the brief AND the orchestrator re-runs the single most-pivotal seat on a sharpened or deliberately neutralized brief (or applies a stated discount to the consensus), rather than only narrating the suspicion.

### Model assignment (optional tuning)
The seat files in this repo ship without a `model:` pin, so each seat inherits your session model. If your setup supports per-agent model pinning (the `model:` frontmatter field), a split that has worked well:
- **Heavier reasoning tier (e.g. Opus):** Chair, Product, Data, Red-Team, Customer Voice (rigor- and refusal-critical, including both blind seats).
- **Faster tier (e.g. Sonnet):** UX, Engineering, QA, Business/GTM (breadth and speed).

Honest note: two tiers of the SAME vendor's model family are only **partial** decorrelation. True cross-vendor decorrelation is usually not available in a single harness. The load-bearing decorrelation is the distinct mandates + blind independence; treat unanimity with suspicion regardless (see the gate).

---

## The readiness gate

GATED if ANY hold:
1. an unresolved refusal or safety flag from any seat;
2. an unresolved BLOCK from any seat at >=75 confidence (a spine-driven BLOCK is a flag, not a minority vote to average away);
3. two seats both >=75 confident pointing OPPOSITE ways, where opposite = one in {APPROVE, APPROVE-WITH-CONDITIONS} and another in {REVISE, BLOCK};
4. the Red-Team's load-bearing premise is one the brief presents as settled-but-unproven and no seat resolved it (a premise merely named becomes a binding condition, not a gate);
5. a blocking seat that was part of the chosen panel returned no parseable verdict (fail-safe: silence from a seat you DID dispatch GATES, never CLEARS). Blocking seats = Red-Team in every panel, plus Data and Customer Voice when selected; a seat deliberately not in the panel does not gate.

Otherwise CLEARS. Never gate on a low AVERAGE confidence; gate on unresolved disagreement and unresolved flags. **Treat unanimity as a warning sign.** ("95% gate" is shorthand for this readiness bar, not a numeric average threshold.)

---

## Confidence (defined once, used everywhere)

Confidence is 0-100 = a seat's estimated probability that its verdict is the right call given the brief, NOT how strongly it feels. ~75 means "I would bet 3:1 I am right." Every seat and the gate use this identically.

## Scope envelope (defined once)

A scope envelope states what a decision DOES and does NOT authorize: the boundary inside which the user can act without re-convening (the changes and conditions covered), and the triggers that require a fresh review. The Chair writes one for every cleared decision.

---

## Output and the review artifact

The Chair's output is the CEO brief (question + seats fired/abstained, convergence X/N, verdict table, sharpest dissent verbatim, refusal/safety flags + resolution, gate verdict + triggering condition, binding conditions, scope envelope, recommendation + strongest reason against).

**Review artifact (when a decision is worth a record):** save the brief to a docs folder in the project, e.g. `docs/council-reviews/<YYYY-MM-DD>-<slug>.md`; in a bare chat or a project with no docs home, present it inline and offer to save it where the user wants. Fields: date, question, seats fired/abstained, convergence, verdict table, dissent verbatim, flags + resolution, gate verdict, binding conditions, scope envelope, final decision + owner.

## Calibration (off by default)

OFF for one-shot work (auditions, case studies): at small N a council "score" is luck-dominated, and the value is the recorded probability, not the score. ON only for long-lived projects that accumulate many resolved predictions. When ON: each cleared decision logs the Chair's recommendation + a 0-100 prediction + a dated outcome-check; at the check, mark CORRECT / INCORRECT / INCONCLUSIVE; compute a Brier score only past roughly 20 resolved predictions (below that it is noise).

## Witnesses (on-call, read-only)

The framing step pre-selects any obvious witness; a seat or the Chair may request one mid-review. A witness is dispatched **read-only** (it returns a written verdict only and must not Write, Edit, or run Bash), gets the same brief, and its answer is handed to the requesting seat or the Chair as **evidence, not a voting verdict**. Any specialist agent installed in your setup can serve — an architecture reviewer, a security reviewer, a database or performance reviewer, or this repo's `growth-hacker` / `seo-specialist` for channel questions — as long as it is dispatched read-only. **Legal / compliance / regulatory risk has no standing seat by design** (the roster is fixed at 9); summon a witness for it when a decision turns on it.

## Time and cost discipline

A full council is roughly 10-15x the tokens of a single pass. Size the panel to the stakes; use the fast path for mid-stakes. Fan out concurrently: each WAVE's wall-clock is its slowest seat, but the protocol has up to three serial waves (fan-out, optional second round, synthesis), so budget for that, not a single seat's latency.

---

## Why the seats carry mandates, not fictional credentials

Many multi-agent setups credential their seats ("PhD from Cambridge") or flavor them after famous companies. This council deliberately **drops fictional credentials**, because the evidence says they do not help:
- Zheng et al., *Findings of EMNLP 2024* ("When a Helpful Assistant Is Not Really Helpful"): role personas do not reliably improve factual correctness, and the best persona cannot be picked in advance.
- Wharton GAIL, 2025 ("Playing Pretend"): expert personas do not reliably improve accuracy on hard benchmarks; low-knowledge or mismatched personas can degrade it and, in their runs, triggered refusals.

The evidence-backed substitute is each seat's **crisp mandate + distinct lens + hard refusal rules**. The legitimate use of "flavor" is to make seats genuinely differ (decorrelation), not to credential any one seat.

Other sources behind the design: Anthropic *Building Effective Agents* and *Multi-Agent Research System* (parallelize + synthesize, add complexity only when it pays); Mixture-of-Agents (a well-aggregated panel beats a single model); *Talk Isn't Always Cheap* (cross-talk erodes correct answers, so keep seats independent); *Nine Judges, Two Effective Votes* (a panel of correlated voting judges delivers far fewer effective votes than its headcount, so the 8 voting seats must be genuinely decorrelated; the Chair does not vote); Annie Duke, *Thinking in Bets* (collect opinions before discussion); Klein, *Pre-mortem* (HBR).
