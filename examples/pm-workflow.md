# A Complete Product Launch with the 7 PM Agents

This walkthrough shows how the 7 agents compose into a single end-to-end launch workflow. Real example, lightly anonymized.

---

## Setup

You're shipping a new feature: a "daily digest" that emails users a morning summary of their tasks. You need a PRD, a launch plan, app-store metadata, content for SEO, a launch video, and an inbox you can keep up with during launch week.

This is what each agent contributes.

---

## Day 1: Discovery + PRD

```
You: @product-manager I want to ship a daily digest feature.
     Help me decide if it's worth building, then write the PRD.

@product-manager:
  Phase 1 — Discovery (run before any solution work):
  - 5-10 problem interviews
  - Mine analytics for the friction this would solve
  - Audit support tickets
  Phase 2 — Opportunity Assessment (RICE):
  - Reach: 8,000 weekly active users
  - Impact: 1.5 (better retention)
  - Confidence: 75% (have signal from 12 user interviews)
  - Effort: 2 person-weeks
  - RICE Score: (8000 * 1.5 * 0.75) / 2 = 4,500 → top of backlog

  Recommendation: Build. Writing PRD now.
  [PRD draft attached: problem, goals, success metrics, user stories, ...]
```

---

## Day 5: Brand check + asset prep

```
You: @brand-guardian we're shipping the daily digest next week.
     Audit the email template against our brand foundation.

@brand-guardian:
  Brand audit checklist:
  ✅ Colors match brand palette
  ❌ Email uses Inter for body but brand voice is Display font
  ❌ Subject line tone is "professional" — brand voice is "playful but precise"
  ✅ Logo usage follows clear-space rules
  ❌ No accessibility check on email colors (WCAG AA fail on muted gray)

  Action items: 4 fixes before send.
```

---

## Day 6: ASO (if mobile) or SEO (if web)

For a web launch:
```
You: @seo-specialist optimize the /daily-digest landing page.

@seo-specialist:
  Keyword strategy:
  Primary: "daily task digest" (320 vol, KD 18)
  Cluster: "morning task summary", "ai daily standup", ...

  Technical fixes:
  - LCP currently 3.4s → needs to be <2.5s
    [specific Core Web Vitals optimizations]
  - Missing schema: Article + FAQ
  - Missing AEO: no FAQ answers will be cited by ChatGPT/Claude
  ...
```

---

## Day 7: Growth experiment design

```
You: @growth-hacker design 3 activation experiments for the daily digest.

@growth-hacker:
  Experiment 1: Onboarding nudge
    Hypothesis: If we promote daily digest in onboarding step 3,
    activation will go from 12% → 25% because users see value before friction.
    Metric: digest opt-in rate at signup
    Effort: S | Confidence: M

  Experiment 2: Email timing A/B
    7am vs 9am send — impact on open rate
    ...

  Experiment 3: Re-engagement push
    Lapsed users (no login in 7 days) get one digest preview email
    ...
```

---

## Day 8: Launch video

```
You: @youtube-optimizer package a 60s launch video for the daily digest.

@youtube-optimizer:
  Title options (CTR-optimized):
  1. "I Was Drowning in Tasks. Then I Built This."  (Curiosity)
  2. "How to Plan Your Day in 30 Seconds"  (Direct)
  3. "Stop Forgetting Action Items From Meetings"  (Benefit)

  Thumbnail concept:
    Visual: Split before/after — chaotic notebook → clean digest email
    Text: "STOP FORGETTING"
    Colors: High contrast, brand-primary on dark gray

  Video structure (60s):
    0:00-0:05 — Hook: "What if your morning email knew exactly what to do today?"
    0:05-0:15 — Problem: meeting notes lost forever
    0:15-0:45 — Demo: show digest landing in inbox
    0:45-0:55 — CTA: try free for 14 days
    0:55-1:00 — End screen → next video
```

---

## Day 9: Launch + inbox triage

Launch day. Slack and email start exploding.

```
You: @chief-of-staff triage everything.

@chief-of-staff:
  📥 Email — 47 unread
    skip: 23 (auto-archived)
    info_only: 8
    meeting_info: 4 (cross-referenced calendar, 2 missing Zoom links — fixed)
    action_required: 12

  Drafting replies for the 12 action_required items, organized by sender priority...

  Draft 1: Sarah (investor) re: launch metrics
    [draft loaded with relationship context + your tone preferences]
    → [Send] [Edit] [Skip]
```

---

## After launch (Week 2-4)

`@product-manager` reviews 30/60/90-day metrics; `@growth-hacker` analyzes experiment results and proposes the next 3.

The cycle repeats.

---

## Why this stacks well

Each agent has its own model, its own toolbox, and its own operating rules — so output stays consistent. The PM agent never tries to do brand work; the brand agent never writes RICE scores. Composition stays clean.

That's the whole point.
