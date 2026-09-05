<div align="center">

# 🔍 Claude Code PM Agents: a product builder's toolkit

### Seven specialists that do the work, and nine that argue about it before I commit.

![requires](https://img.shields.io/badge/requires-Claude%20Code-D97757) ![agents](https://img.shields.io/badge/agents-7%20%2B%209%20council-0E9384) ![seats](https://img.shields.io/badge/dissent-surfaced%20verbatim-1B2A4A) ![code](https://img.shields.io/badge/no%20code-markdown%20only-lightgrey) ![license](https://img.shields.io/badge/license-MIT-green)

</div>

---

Claude Code is Anthropic's AI coding assistant. It runs in your terminal, reads the files in your
project, and can change them for you. A **subagent** is a separate assistant with one job and its own
instructions, which you call by name.

Seven of these do product work, one mode each, so I stop context-switching between them. The other 9
are a council that pressure-tests a decision before I commit to it.

<img src="docs/how-the-council-works.svg" width="100%"
     alt="Why nine seats instead of one 'review this'. One neutral brief, worded to lead nobody, goes to eight seats that answer in parallel: product, ux, engineering, data, qa and gtm, while the red-team and customer seats answer blind, seeing no one else's reply. A chair, which does not vote, reads them in random order, prints disagreement word for word, and never averages it into a verdict. Everyone agreeing is treated as a warning rather than a pass, and you decide, always. The one time I measured it: six agents, four of them from this bundle, reviewed a release build of my Android app, all six approved with conditions averaging 77 out of 100, and then I found a critical bug all six had missed, which is why the gate only advises. The seven agents that do the work rather than judge it are listed further down the page.">

## Why a panel and not one "review this"

Ask one assistant to consider every angle and it tends to average itself into a reasonable-sounding
paragraph. Nine seats with different mandates surface the disagreement instead of smoothing it, and
the chair is required to print that disagreement word for word rather than resolve it for you.

A **seat** is one of those nine, and it's just a markdown file: a mandate, and a list of things it
won't wave through however the question is put to it. The **charter** is the governance document that
ships alongside them, in `agents/council/CHARTER.md`, and it's what sets the rules below.

**The honest counter-argument, which the charter cites in full:** a panel whose members make the same
mistakes on the same questions isn't nine opinions, it's closer to two, and a single strong reviewer
can match it. Nothing here is enforced by code either. These are instructions to a model, not
permissions a runtime checks, and the seats share a model, so "independent" is a design intent rather
than a guarantee.

So what this buys is structured dissent, not a better score, and the one time I measured it is on the
picture above: six agents reviewed a release build of my Android app, four of them from this bundle
and two others I keep globally. All six approved with conditions. Then I found a critical bug all six
had missed. That's an n of one and it went against the design, which is exactly why the gate only
advises and you decide.

A full council also runs roughly 10 to 15 times the tokens of a single pass, so it's for the calls
where being wrong is expensive. The charter defines a faster path of three seats plus the chair, and
for a question with one right answer you skip it entirely.

<details>
<summary><b>📋 The seven agents that do the work</b></summary>

Each is one markdown file with its own tool allowlist and its own model tier, so a heavier reasoning
job gets a heavier model and the app-store agent can't accidentally run a shell command.

| Agent | Model | Job | Use when |
|-------|-------|-----|----------|
| `product-manager` | opus | Product specs (PRDs), RICE prioritisation (reach, impact, confidence, effort), roadmaps, go-to-market (GTM) briefs | Defining or prioritising any product work |
| `growth-hacker` | sonnet | Experiment design, funnel optimisation, launch playbooks | Pre-launch, post-launch, or growth-stuck |
| `brand-guardian` | sonnet | Brand foundation, visual identity, platform consistency | Setting up a new product, or auditing brand drift |
| `aso-specialist` | sonnet | App store optimisation (ASO): getting found in the Play Store and Chrome Web Store | Before any app store submission |
| `seo-specialist` | sonnet | Technical search optimisation (SEO), topic clusters, and being quotable by AI search engines (AEO) | Any content site or product web app |
| `youtube-optimizer` | sonnet | YouTube titles, thumbnails, retention, channel strategy | Any video workflow |
| `chief-of-staff` | opus | Triage across email, Slack, LINE and Messenger, plus draft replies. Needs a Gmail command-line tool, Node.js, its own knowledge files, a calendar script and a Claude Code hook, none of which ship in this repo. | Daily inbox triage |

</details>

<details>
<summary><b>🔍 The nine council seats, and the rules they run under</b></summary>

Eight voting discipline seats plus a chair who doesn't vote.

| Seat | Brings |
|------|--------|
| `council-product` | Validated value and viability (Cagan's four risks), outcome over output |
| `council-ux` | Nielsen's usability heuristics, error and empty states, accessibility |
| `council-engineering` | The riskiest technical assumption first, build-versus-buy, scale |
| `council-data` | Denominators, base rates, significance, and guardrail metrics |
| `council-qa` | Operational failure modes (FMEA), a pre-mortem, and blast radius |
| `council-redteam` | Attacks the premise itself. Answers blind |
| `council-customer` | The real buyer and the job they're hiring you for (JTBD). Refuses invented quotes and stats. Answers blind |
| `council-gtm` | Bottom-up sizing, cost to acquire against lifetime value (CAC/LTV), willingness to pay, and the moat |
| `council-chair` | Synthesises, surfaces dissent verbatim, applies the readiness gate. Does not vote |

**How it resists agreeing with itself:**

- **Voting seats answer independently and blind.** Each gets the same neutralised brief. Red-Team and
  Customer never see other verdicts, because a skeptic who reads the consensus mirrors it, and a
  customer voice filling gaps invents things. **One honest caveat:** Claude Code subagents still
  inherit the repo's `CLAUDE.md` and memory, so the seats aren't fully isolated from project context.
  Keep that context neutral if you need real blindness.
- **Each seat has a refusal spine:** things it won't pass however the brief is framed. Customer Voice
  labels every unverified claim as a hypothesis.
- **Verdicts reach the chair in randomised order** to blunt position bias. Randomising helps with
  order effects; it can't remove framing, salience, or the fact that the models share a brain.
- **Abstaining seats are excluded from the agreement count** rather than counted as a yes, and a
  blocking seat that says nothing gates rather than clears.
- **The gate is advisory.** It flags unresolved blocks, high-confidence forks, and silence. Unanimity
  is a warning sign, not a pass.

The counter-evidence the charter cites in full: *"Nine Judges, Two Effective Votes: Correlated Errors
Undermine LLM Evaluation Panels"* (Apple ML Research, 2026, arXiv:2605.29800). A panel of correlated
judges delivers far fewer effective votes than its headcount, and the best single judge can match or
beat the whole panel. That's why the eight seats are built to be genuinely different from each other,
and why nothing here claims a panel scores better than one strong review.

</details>

## Install

You need [Claude Code](https://claude.com/claude-code) itself first. `~/.claude/` is the folder it
keeps its own settings in.

```bash
git clone https://github.com/aksheyw/claude-code-pm-agents.git
cd claude-code-pm-agents

# The seven lifecycle agents
mkdir -p ~/.claude/agents
cp -i agents/*.md ~/.claude/agents/

# The council, if you want it: nine seats, the charter, the runbook
cp -i agents/council/council-*.md ~/.claude/agents/
mkdir -p ~/.claude/council ~/.claude/skills/council-full
cp -i agents/council/CHARTER.md ~/.claude/council/CHARTER.md
cp -i skills/council-full/SKILL.md ~/.claude/skills/council-full/SKILL.md
```

`cp -i` asks before replacing a file you already have. The seat files go into `~/.claude/agents/`
flat, alongside the others, since `agents/council/` is only how this repo is organised.

Then, in any session:

```
@product-manager write a spec for a daily-digest feature
@growth-hacker design 3 activation experiments
convene the council on whether to build X
```

Or just describe the task and let Claude Code route it, using the `description:` line in each file.

<details>
<summary><b>🔍 Check it worked, and what to do if it didn't</b></summary>

Start a fresh session, type `@`, and autocomplete should list all seven. The `/agents` view should
show them under user-level agents, plus the nine `council-*` seats if you installed those. Ask
*"design 3 activation experiments"* and it should route to `@growth-hacker` on its own.

- **`@<agent>` doesn't autocomplete:** check the files landed flat at `~/.claude/agents/<name>.md`,
  not in a subdirectory, then restart the session, because agents load at session start.
- **It routes to the wrong agent:** routing reads the `description:` line in the file. Edit it to
  mention the kind of task you're giving it.
- **Wrong model:** check the `model:` line. `product-manager` and `chief-of-staff` are `opus`, the
  rest `sonnet`. The council seats deliberately ship with no pin, so they inherit your session model;
  the charter suggests a tier split if you'd rather pin them.
- **It ran but ignored its templates:** name the template, as in *"use the spec template"*. Some
  sessions short-circuit the full workflow.

</details>

<details>
<summary><b>📄 What comes out: the first lines of a real spec</b></summary>

Abbreviated `@product-manager` output for a daily-digest feature.

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

The numbers above are illustrative. What the agent enforces on every spec is the shape:
explicit non-goals and a guardrail metric that kills the feature if it's breached. No
hand-waved goals.

</details>

<details>
<summary><b>📦 A whole launch, agent by agent</b></summary>

A new feature runs through the chain:

1. `@product-manager` writes the spec: problem, goals, success metrics
2. *Engineering builds it*
3. `@brand-guardian` checks the launch assets stay on-brand
4. `@aso-specialist` for mobile, or `@seo-specialist` for web, works on getting it found
5. `@growth-hacker` designs the launch playbook and the first few experiments after it
6. `@youtube-optimizer` packages the announcement video, if there is one
7. `@chief-of-staff` keeps the inbox triaged so you can focus on the launch

Full walkthrough in [examples/pm-workflow.md](examples/pm-workflow.md).

**Editing them:** each agent is one markdown file with frontmatter (name, description,
tool allowlist, model), a role section, its non-negotiable rules, its templates, and its workflow.
The structure is opinionated and the content is meant to be changed. They lean direct and
metric-first, so if you want a softer tone, edit the rules section.

</details>

## Companion repos

Part of my Claude Code config series:

- [`claude-code-deep-review`](https://github.com/aksheyw/claude-code-deep-review): a 14-lens iterative review skill, a good companion to `product-manager` for spec review
- [`claude-code-rules`](https://github.com/aksheyw/claude-code-rules): the 13 global rules these agents operate under
- [`claude-code-learned-skills`](https://github.com/aksheyw/claude-code-learned-skills): 12 skills taken from real debugging and research sessions, covering Docker, SSH and VPS work, ML pipelines, prompting guides and a project wiki
- [`career-command-center-template`](https://github.com/aksheyw/career-command-center-template): a full plugin template for running a job search with Claude Code, with 12 skills, 8 personal-data files you fill in yourself, and hooks

## License

MIT. See [LICENSE](LICENSE).

---

Built by [Akshey Walia](https://github.com/aksheyw). If you build something on top of these, or add an
agent, send a pull request.
