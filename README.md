# Claude Code PM Agents — A Product Builder's Toolkit

> **Seven Claude Code subagents that cover the full product-builder lifecycle: PRDs, growth, brand, ASO, SEO, YouTube, and comms triage.**

This is the bundle of agents I use to run product work end-to-end with Claude Code. Each agent is a focused specialist with its own model, tools, and operating principles — invoke them individually or compose them into a workflow.

## Why I built this

I ship side-projects regularly and kept catching myself context-switching between four different "modes" — PRD writing, growth experiments, brand decisions, app-store optimization — and losing time on each switch.

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

## What makes them useful (vs writing the prompt yourself)

- **Each agent has a strict operating model.** Hard rules, templates, and success metrics baked in — so output is consistent across sessions.
- **Each agent has its own tool allowlist.** SEO has Bash for crawl audits; ASO doesn't. Reduces accidents.
- **Each agent has its own model tier.** PM and chief-of-staff use Opus (heavier reasoning); execution agents use Sonnet (faster, cheaper).
- **They compose.** A typical product launch routes through 4 of them in sequence — example below.

## Install

```bash
git clone https://github.com/aksheyw/claude-code-pm-agents.git
cd claude-code-pm-agents

# Drop all agents into your Claude Code config
cp agents/*.md ~/.claude/agents/
```

After install, in any Claude Code session:

```
@product-manager write a PRD for a daily-digest feature
@growth-hacker design 3 activation experiments
@aso-specialist optimize my Play Store listing
```

…or let Claude Code auto-route based on the task description. The `description:` field in each agent's frontmatter tells Claude when to use it.

## Verify install worked

In a fresh Claude Code session:

- Type `@` — autocomplete should list all 7 agents (`@product-manager`, `@growth-hacker`, `@brand-guardian`, `@aso-specialist`, `@seo-specialist`, `@youtube-optimizer`, `@chief-of-staff`).
- Or open the `/agents` UI — all 7 should appear under user-level agents.
- Ask: *"design 3 activation experiments"* → Claude should auto-route to `@growth-hacker` via description matching.

If `@` doesn't show them, see **Troubleshooting** below.

## Example output

Abbreviated `@product-manager` output for a daily-digest feature PRD — first ~15 lines of a longer PRD that ships with problem statement, goals, success metrics, RICE score, GTM brief, and rollout plan.

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

The agent enforces RICE scoring, explicit non-goals, and a guardrail metric on every PRD — no fabricated numbers, no hand-wave goals.

## Troubleshooting

- **`@<agent>` doesn't autocomplete:** confirm files landed at `~/.claude/agents/<name>.md` (flat, not in a subdir). Restart your Claude Code session — agents load at session start.
- **Auto-routing doesn't pick the right agent:** the routing depends on the `description:` frontmatter in each agent file. If you find a particular task isn't routing, edit that agent's `description:` to mention the task pattern.
- **Wrong model is being used:** check the `model:` field in the agent's frontmatter. PM and chief-of-staff default to `opus`; rest are `sonnet`. Override per session by passing `--model` to Claude Code.
- **Agent ran but skipped its templates:** mention the template explicitly (e.g., *"use the PRD template"*) — some sessions short-circuit the agent's full workflow.

## Example: a complete product launch workflow

A new feature goes through this chain:

1. `@product-manager` — writes the PRD (problem, goals, RICE score, success metrics)
2. *Engineering builds it*
3. `@brand-guardian` — verifies the launch assets stay on-brand
4. `@aso-specialist` (if mobile) or `@seo-specialist` (if web) — optimizes the discoverability surface
5. `@growth-hacker` — designs the launch playbook + 3-5 post-launch growth experiments
6. `@youtube-optimizer` (if there's a launch video) — packages the announcement video
7. `@chief-of-staff` — keeps the inbox triaged so you can focus on the launch

Full walkthrough: [examples/pm-workflow.md](examples/pm-workflow.md)

## Customizing the agents

Each agent file is one self-contained Markdown document with:
- **Frontmatter** — name, description, tool allowlist, model
- **Role section** — what the agent does
- **Critical Rules** — the non-negotiable principles
- **Templates** — PRD format, RICE table, launch checklist, etc.
- **Workflow** — phased steps from discovery to measurement

Edit any of these to fit your product's context. The structure is opinionated; the content is meant to be customized.

## Tone / voice

These agents lean direct, opinionated, and metric-first. If your team prefers a softer tone, edit the "Critical Rules" sections.

## Companion repos

These agents are part of my Claude Code config series:
- [`claude-code-deep-review`](https://github.com/aksheyw/claude-code-deep-review) — 14-lens iterative review skill; great companion to the `product-manager` agent for PRD review and the `growth-hacker` agent for launch readiness
- [`claude-code-rules`](https://github.com/aksheyw/claude-code-rules) — opinionated global rules these agents operate under (commit format, branch strategy, honesty/earned-confidence)
- [`claude-code-learned-skills`](https://github.com/aksheyw/claude-code-learned-skills) — 3 Docker / SSH / VPS skills auto-extracted from real debugging sessions
- [`career-command-center-template`](https://github.com/aksheyw/career-command-center-template) — full plugin template for an AI-native job-search workflow (10 skills, 5 personal-data skeletons, hooks)

## License

MIT — see [LICENSE](LICENSE).

---

Built by [Akshey Walia](https://github.com/aksheyw). If you build interesting workflows on top of these or extend them with new agents, send a PR.
