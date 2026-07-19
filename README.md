# Claude Code PM Agents: A Product Builder's Toolkit

> **Seven Claude Code subagents that cover the full product-builder lifecycle: PRDs, growth, brand, ASO, SEO, YouTube, and comms triage, plus a 9-seat product council that pressure-tests high-stakes decisions.**

This is the bundle of agents I use to run product work end-to-end with Claude Code. Each agent is a focused specialist with its own model, tools, and operating principles, so you can invoke them individually or compose them into a workflow. v2 adds the council: a structured multi-agent deliberation system for the decisions where being wrong is expensive.

## Why I built this

I ship side-projects regularly and kept catching myself context-switching between four different "modes" (PRD writing, growth experiments, brand decisions, app-store optimization) and losing time on each switch.

I broke each mode into its own subagent so I could just say "@product-manager write the PRD" or "@growth-hacker design 3 experiments for activation" and get focused output without re-establishing context.

This is that bundle, refined across multiple product launches.

## The 7 agents

| Agent | Model | Job | Use when |
|-------|-------|-----|----------|
| `product-manager` | opus | PRDs, RICE scoring, roadmaps, GTM briefs | Defining or prioritizing any product work |
| `growth-hacker` | sonnet | Experiment design, funnel optimization, launch playbooks | Pre-launch, post-launch, or growth-stuck |
| `brand-guardian` | sonnet | Brand foundation, visual identity, platform consistency | Setting up a new product or auditing brand drift |
| `aso-specialist` | sonnet | Play Store + Chrome Web Store optimization | Before any app store submission |
| `seo-specialist` | sonnet | Technical SEO, topic clusters, AI-search (AEO) | Any content site or product web app |
| `youtube-optimizer` | sonnet | Titles, thumbnails, retention, channel strategy | Any video / YouTube workflow |
| `chief-of-staff` | opus | Multi-channel comms triage (email, Slack, LINE, Messenger) + draft replies | Daily inbox / Slack triage |

## New in v2: the 9-seat product council

The seven agents above do the work. The council judges it. It is a structured deliberation system for high-stakes calls (interview case studies, 0-1 bets, positioning, monetization, go/no-go) where a single "review this" prompt tends to nod along.

**What ships:** 9 seat definitions ([`agents/council/`](agents/council/)), a governance charter ([`agents/council/CHARTER.md`](agents/council/CHARTER.md)), and an orchestration runbook skill ([`skills/council-full/SKILL.md`](skills/council-full/SKILL.md)). Eight voting discipline seats plus a non-voting Chair:

| Seat | Brings |
|------|--------|
| `council-product` | validated value + viability (Cagan's four risks), outcome over output |
| `council-ux` | Nielsen heuristics, error/empty states, accessibility |
| `council-engineering` | riskiest technical assumption first, build-vs-buy, scale |
| `council-data` | denominators, base rates, significance, guardrail metrics |
| `council-qa` | operational failure modes (FMEA), pre-mortem, blast radius |
| `council-redteam` | attacks the premise itself; answers blind |
| `council-customer` | the real buyer (JTBD); refuses invented quotes and stats; answers blind |
| `council-gtm` | bottom-up sizing, CAC/LTV, willingness-to-pay, moat |
| `council-chair` | synthesizes verdicts, surfaces dissent verbatim, applies the readiness gate; does not vote |

**The design bet: structured dissent, not a proven better score.** This is a design hypothesis, not an established result. The rationale: ask one model to "consider all angles" and it tends to average itself into a consensus paragraph, so separate seats with distinct mandates surface the disagreement instead of smoothing it over. The honest counter-evidence: correlated LLM judge panels can merely match (or even underperform) the best single judge, because the models make the same mistakes on the same items (see *Nine Judges, Two Effective Votes*, cited in the charter). So the benefit claimed here is **structured dissent and explicit disagreement-surfacing**, not "a panel scores better than one strong review." The council is built for that:

- **Seats answer independently and blind**: each seat gets the shared neutralized brief as its task input, and the Red-Team and Customer Voice seats never see other verdicts (a skeptic who sees the consensus mirrors it; a customer voice that fills gaps fabricates). One honest caveat: Claude Code subagents still inherit the repo's CLAUDE.md and memory, so seats are not fully isolated from project context. Keep that context neutral if you need true blindness.
- **Each seat has a distinct mandate and a hard refusal spine**: things it will not pass no matter how the brief is framed. The Customer Voice seat refuses invented quotes and satisfaction stats and labels every unverified claim [hypothesis].
- **The Chair surfaces dissent verbatim, never averages it away.** Verdicts reach the Chair in randomized order to reduce position bias (randomization mitigates systematic order effects; it can't remove framing, salience, or shared-model correlation), and abstaining seats are excluded from the convergence denominator instead of counted as agreement.
- **A readiness gate, advisory only.** The gate flags unresolved BLOCKs, high-confidence forks, and silence from a blocking seat (silence gates, never clears). Unanimity is treated as a warning sign, not a pass. You always decide; the council never does.

**When to convene it.** Full panel (8 seats + Chair) only for the genuinely high-stakes, multi-path calls. For mid-stakes single deliverables, the charter defines a fast path: Product + Red-Team + the one most-relevant domain seat + Chair. For trivial or one-right-answer questions, skip it entirely, since a full council runs roughly 10-15x the tokens of a single pass, and the charter is explicit that a council firing on everything becomes a tax, not an edge.

**Invoke it:** after install, say *"convene the council on [decision]"* or load the `council-full` skill. The skill is the runbook; the charter is the governance.

## What makes them useful (vs writing the prompt yourself)

- **Each agent has a strict operating model.** Hard rules, templates, and success metrics baked in, so output is consistent across sessions.
- **Each agent has its own tool allowlist.** SEO has Bash for crawl audits; ASO doesn't. Reduces accidents.
- **Each agent has its own model tier.** PM and chief-of-staff use Opus (heavier reasoning); execution agents use Sonnet (faster, cheaper).
- **They compose.** A typical product launch routes through 5 or 6 of them in sequence (example below).

## Install

```bash
git clone https://github.com/aksheyw/claude-code-pm-agents.git
cd claude-code-pm-agents

# Drop the 7 lifecycle agents into your Claude Code config
cp agents/*.md ~/.claude/agents/

# Council (optional): the 9 seats, the charter, and the runbook skill
cp agents/council/council-*.md ~/.claude/agents/
mkdir -p ~/.claude/council ~/.claude/skills/council-full
cp agents/council/CHARTER.md ~/.claude/council/CHARTER.md
cp skills/council-full/SKILL.md ~/.claude/skills/council-full/SKILL.md
```

Note the seat files go into `~/.claude/agents/` flat, same as the lifecycle agents (the `agents/council/` subdirectory is just repo organization). The council seats ship without a `model:` pin (they inherit your session model); the charter's "Model assignment" section suggests a tier split if your setup pins models per agent.

After install, in any Claude Code session:

```
@product-manager write a PRD for a daily-digest feature
@growth-hacker design 3 activation experiments
@aso-specialist optimize my Play Store listing
```

…or let Claude Code auto-route based on the task description. The `description:` field in each agent's frontmatter tells Claude when to use it.

## Verify install worked

In a fresh Claude Code session:

- Type `@` and autocomplete should list all 7 agents (`@product-manager`, `@growth-hacker`, `@brand-guardian`, `@aso-specialist`, `@seo-specialist`, `@youtube-optimizer`, `@chief-of-staff`).
- Or open the `/agents` UI, where all 7 should appear under user-level agents.
- Ask: *"design 3 activation experiments"* → Claude should auto-route to `@growth-hacker` via description matching.
- If you installed the council: `/agents` should also list the 9 `council-*` seats, and *"convene the council on whether to build X"* should load the `council-full` skill and fan out seats in parallel.

If `@` doesn't show them, see **Troubleshooting** below.

## Example output

Abbreviated `@product-manager` output for a daily-digest feature PRD: the first ~15 lines of a longer PRD that ships with problem statement, goals, success metrics, RICE score, GTM brief, and rollout plan.

```
# PRD: Daily Digest Feature

## Problem
Active users open the app 4x/day but engage with new content only 20% of those sessions —
the discovery surface is buried two taps deep. Engagement decays at -3pp/week after week 6.

## Goal
Lift D7 engagement from 47% → 55% within 6 weeks of launch.

## Success metrics (in priority order)
1. D7 engaged_user_rate ≥ 55% (primary)
2. Notification CTR ≥ 18% (secondary — lower bound; below this we kill it)
3. Unsubscribe rate ≤ 4% (guardrail)

## RICE score
- Reach: 280k MAU · Impact: 2 (moderate-high) · Confidence: 70% · Effort: 8 weeks
- Score: 280 × 2 × 0.7 / 8 = 49 — top quartile this quarter

## Non-goals (explicit)
- Personalization beyond top-3 saved categories (defer to v2)
- Push frequency tuning per user (defer until baseline data lands)
```

The agent enforces RICE scoring, explicit non-goals, and a guardrail metric on every PRD: no fabricated numbers, no hand-wave goals.

## Troubleshooting

- **`@<agent>` doesn't autocomplete:** confirm files landed at `~/.claude/agents/<name>.md` (flat, not in a subdir). Restart your Claude Code session, because agents load at session start.
- **Auto-routing doesn't pick the right agent:** the routing depends on the `description:` frontmatter in each agent file. If you find a particular task isn't routing, edit that agent's `description:` to mention the task pattern.
- **Wrong model is being used:** check the `model:` field in the agent's frontmatter. PM and chief-of-staff default to `opus`; rest are `sonnet`. Override per session by passing `--model` to Claude Code.
- **Agent ran but skipped its templates:** mention the template explicitly (e.g., *"use the PRD template"*), because some sessions short-circuit the agent's full workflow.

## Example: a complete product launch workflow

A new feature goes through this chain:

1. `@product-manager`: writes the PRD (problem, goals, RICE score, success metrics)
2. *Engineering builds it*
3. `@brand-guardian`: verifies the launch assets stay on-brand
4. `@aso-specialist` (if mobile) or `@seo-specialist` (if web): optimizes the discoverability surface
5. `@growth-hacker`: designs the launch playbook + 3-5 post-launch growth experiments
6. `@youtube-optimizer` (if there's a launch video): packages the announcement video
7. `@chief-of-staff`: keeps the inbox triaged so you can focus on the launch

Full walkthrough: [examples/pm-workflow.md](examples/pm-workflow.md)

## Customizing the agents

Each agent file is one self-contained Markdown document with:
- **Frontmatter**: name, description, tool allowlist, model
- **Role section**: what the agent does
- **Critical Rules**: the non-negotiable principles
- **Templates**: PRD format, RICE table, launch checklist, etc.
- **Workflow**: phased steps from discovery to measurement

Edit any of these to fit your product's context. The structure is opinionated; the content is meant to be customized.

## Tone / voice

These agents lean direct, opinionated, and metric-first. If your team prefers a softer tone, edit the "Critical Rules" sections.

## Companion repos

These agents are part of my Claude Code config series:
- [`claude-code-deep-review`](https://github.com/aksheyw/claude-code-deep-review): 14-lens iterative review skill; great companion to the `product-manager` agent for PRD review and the `growth-hacker` agent for launch readiness
- [`claude-code-rules`](https://github.com/aksheyw/claude-code-rules): opinionated global rules these agents operate under (commit format, branch strategy, honesty/earned-confidence)
- [`claude-code-learned-skills`](https://github.com/aksheyw/claude-code-learned-skills): 12 skills auto-extracted from real debugging and research sessions (Docker/SSH/VPS, ML pipelines, prompting, quality tooling, a project wiki)
- [`career-command-center-template`](https://github.com/aksheyw/career-command-center-template): full plugin template for an AI-native job-search workflow (12 skills, 8 personal-data skeletons, hooks)

## License

MIT. See [LICENSE](LICENSE).

---

Built by [Akshey Walia](https://github.com/aksheyw). If you build interesting workflows on top of these or extend them with new agents, send a PR.
