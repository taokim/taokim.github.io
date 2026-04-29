---
title: "The Machine: AI Evaluating AI-Assisted Code"
date: 2026-02-24
draft: true
description: "366 candidates, 7-stage automated pipeline, 17 scoring model changes, 26 resets. How we built a machine where AI evaluates AI-assisted code at scale — and learned that the engineering scaffolding matters as much as the AI itself."
tags: ["ai", "hiring", "evaluation-pipeline", "engineering-culture", "automation"]
categories: ["Engineering"]
series: ["AI Native Hiring"]
ShowToc: true
TocOpen: false
---

> **⚠️ This post has been superseded.** A revised and consolidated version is available: [The Machine: AI Evaluating AI-Assisted Code](/posts/2026-04-14-ai-native-hiring-part2-the-machine/). This version is kept for reference.

> 🇰🇷 [한국어 버전 읽기](/posts/2026-02-24-ai-native-hiring-machine.ko/)
>
> This is Part 2-1 of the AI-native hiring series.
> [**Part 1**: "The Philosophy"](/posts/2026-02-24-ai-native-hiring-philosophy/) | **Part 2-2**: ["Under the Hood"](/posts/2026-02-24-ai-native-hiring-under-the-hood/) | **Part 3**: "The Human" (coming soon)

Co-written with [이명훈](https://medium.com/@myunghoon.lee), who built and operated the evaluation pipeline.

In [Part 1](/posts/2026-02-24-ai-native-hiring-philosophy/), I laid out the philosophy: LeetCode is dead, the skill has shifted from *how to implement* to *what to implement*, and we designed deliberately ambiguous problems to surface that shift. But philosophy without execution is just a manifesto.

This post is about the machine we built to put that philosophy into practice — an automated pipeline where AI evaluates AI-assisted code across 366 candidates. It's also about what surprised us: **the AI is important, but the engineering around the AI is just as much work.**

---

## The Scale Problem

One candidate's submission takes about 30 minutes to review thoroughly. Source code, documentation, prompts, git history, test coverage, data design. 366 candidates × 30 minutes = 184 hours. One person working full-time for over a month.

But time isn't even the real problem. Three structural limits make manual review fail at this scale:

**Consistency.** Reviewer A grades generously on code quality; Reviewer B is strict. Reviewer C is sharp at 9am and lenient by 4pm. The 200th submission gets different treatment than the 20th. At 366 candidates, these biases compound into noise.

**Partial credit.** This is the subtle one. A candidate whose Docker build fails gets 0 on functional tests — but their source code might show exceptional design thinking. Another candidate's app runs perfectly, but the code is a thinly-understood AI-generated scaffold. How do you evaluate what a human *understands* versus what their AI *produced*? Binary pass/fail destroys this signal.

**Speed.** Hiring timelines don't wait. We needed to evaluate, rank, identify interview candidates, generate interview guides, and schedule — all within weeks, not months.

We needed a system that was automated, consistent, and capable of nuanced multi-dimensional scoring. So we built one.

## Why an AI Agent, Not Custom Code

The first instinct was to build everything from scratch — Python scripts for Docker orchestration, API calls to an LLM for code review, custom parsers for results. But the scope was staggering. A single evaluation requires: git clone, commit deadline checking, security scanning, format validation, code reading and scoring across 8 dimensions, Docker build and startup, health check, 25 functional test cases via curl, scoring, report generation, external system updates, and cleanup. Multiply by 366.

Building all of this as traditional software would have been a project unto itself — with its own bugs, tests, deployment, and maintenance. And we didn't have months. We had weeks.

The key insight: **we didn't need to build an agent. We needed to build instructions for one.**

Modern AI agents can already read code, execute shell commands, build Docker containers, call external APIs, and manage file systems. What they can't do on their own is follow a specific evaluation procedure consistently across 366 candidates.

So instead of writing evaluation *software*, we wrote evaluation *instructions* — scoring criteria, test case definitions, execution procedures — as version-controlled markdown files. The agent reads these files and executes them. Change the scoring weights? Edit the markdown and push. Add a new test case? Update the scenario file. The instructions *are* the program.

This is what we started calling "Markdown as Code." The scoring rubric, the test case definitions, the execution flow — all living in text files that are human-readable, version-controlled, and diff-able. When we changed the scoring model (which happened 17 times), we could see exactly what changed in a git diff. When something scored wrong, we could trace it back to a specific line in the rubric.

The separation was clean: the agent provides the *capabilities* (code reading, Docker, shell, API calls). We provide the *instructions* (what to evaluate, how to score, what format to output). This let us iterate on the evaluation criteria at the speed of editing a document, not the speed of deploying software.

## The 7-Stage Pipeline

Each candidate's submission flows through 7 stages. Every stage produces structured JSON (so any stage can be re-run independently) and updates external state (for dashboards and monitoring).

{{< mermaid title="The 7-Stage Evaluation Pipeline" >}}
graph LR
    I["init<br/>Git clone<br/>Deadline check"] --> S0["stage0<br/>Security scan"]
    S0 --> S1["stage1<br/>Format validation"]
    S1 --> S2["stage2<br/>AI quality eval<br/>(~120 pts)"]
    S2 --> S3["stage3<br/>Docker functional test<br/>(100 pts)"]
    S3 --> SC["scoring<br/>3-Tier calculation"]
    SC --> R["report<br/>Final report<br/>+ notifications"]

    style S2 fill:#2563eb,stroke:#333,color:#fff
    style S3 fill:#16a34a,stroke:#333,color:#fff
{{< /mermaid >}}

**Init.** Clone the candidate's repository. Check the commit timestamps against the submission deadline. Checkout the last valid commit.

**Stage 0 — Security scan.** Candidates submit arbitrary code that we run inside Docker containers. Before anything else, we scan for shell injection, unauthorized network requests, and embedded secrets. This is a binary gate: pass or fail.

**Stage 1 — Format validation.** Does the submission have a README? Source directories? Build configuration? If a candidate can't organize their deliverables, that's a signal — but it's a gate, not a score.

**Stage 2 — AI quality evaluation (~120 pts).** This is where AI reads the candidate's code. The agent evaluates 8 dimensions: prompt quality, agent instructions, requirement derivation, data design, code quality, test quality, git history, and additional implementations. Every score must be backed by specific evidence — a file path, a line number, a code quote. No vibes-based grading.

**Stage 3 — Docker functional testing (100 pts).** This is where the machine runs the code. Build the Docker container, start the application, wait for the health check, then run 25 test cases covering 8 categories. Concurrency tests are repeated 2-3 times for statistical reliability. Each test case uses laddered scoring — not pass/fail, but a spectrum from "no attempt" to "exceptional."

The dual nature is intentional. Stage 2 asks: *does the candidate understand what they built?* Stage 3 asks: *does what they built actually work?* You need both. A candidate can write brilliant design documents but ship broken code. Another can ship working code without understanding why it works. The machine catches both patterns.

**Scoring.** Combine Stage 2 and Stage 3 results through the 3-Tier model (more on this below).

**Report.** Generate the final evaluation report, update the dashboard, send notifications, clean up Docker resources.

## The Scoring Model: 17 Changes, 26 Resets

The scoring model wasn't designed upfront. It was discovered through iteration — 17 model changes and 26 full resets of evaluation results over 36 days.

### From Pass/Fail to Laddered Scoring

We started with binary pass/fail on each test case. It failed immediately. Two candidates both "pass" the concurrency test — but one handles it perfectly every time, while the other succeeds inconsistently. Same pass, completely different capabilities.

So we moved to **laddered integer scoring**: each test case has 5-6 named levels, each mapping to a specific point value. The concurrency race test (TC-A1), for example:

| Level | Points | Criterion |
|-------|--------|-----------|
| 5 | 10 | 3 of 3 runs: exactly 1 success + clear error responses |
| 4 | 8 | 3 of 3 runs: exactly 1 success |
| 3 | 6 | 2 of 3 runs exact |
| 2 | 4 | 1-2 successes consistently (not exact but controlled) |
| 1 | 2 | Partial control (2-9 succeed) |
| 0 | 0 | No concurrency control |

"Almost got it right" earns partial credit. This captures a spectrum that binary scoring destroys.

### From Flat Scoring to 3-Tier

Laddered scoring solved the test case problem but revealed a bigger one: **a candidate whose build fails gets 0 on all functional tests, regardless of code quality.** We had candidates with exceptional design thinking — thoughtful architecture, well-structured prompts, clear requirement derivation — who scored zero because their Gradle version was wrong. Throwing them in the same bucket as candidates who submitted empty projects felt deeply unfair.

This forced the separation that became the backbone of our evaluation:

```
Base  = Tier 1 (Make it Work, 6 pts) + Tier 2 (Basic Features, 94 pts) = 100 pts
Depth = Tier 3 (Deep Thought, ~120 pts)
Total = Base + Depth ≈ 220 pts
```

**Base** comes from Stage 3 — Docker functional testing. Does the code *work*?

**Depth** comes from Stage 2 — AI code review. Does the candidate *understand*?

The key: these are scored independently. A build failure (Base = 0) doesn't zero out Depth. A candidate with broken Docker but brilliant code design still gets their Depth score — and might still earn an interview.

### The Compensation Formula

Pure separation created its own problem: a candidate with exceptional depth (say, 100/120) but mediocre base (say, 70/100) ranked below a candidate with high base (90) but shallow depth (50). But intuitively, the first candidate — deep understanding, slightly below-average execution — seemed more promising.

After several iterations, we landed on a compensation formula:

```
effective_base = base + max(0, depth - depth_requirement) / 2
```

The `/2` is critical. Depth surplus converts to base credit at a 2:1 ratio. Exceptional thinking can partially compensate for functional gaps — but not 1:1. You still need to ship working code. And a floor mechanism prevents compensation from kicking in when the base score is too low.

### The 7 Ranks

The scoring model produces a total score, but a single number isn't enough. Two candidates with 150 points might look completely different: one scored 90 Base + 60 Depth, the other scored 60 Base + 90 Depth. So we introduced a rank system that captures the *shape* of the score, not just the magnitude:

| Rank | Base requirement | Depth requirement | Signal |
|------|-----------------|-------------------|--------|
| **Ace** | ≥ 82 | ≥ 80 | Immediate hire recommendation |
| **Craftsman** | ≥ 82 | ≥ 45 | Hire recommendation |
| **Hustler** | ≥ 82 | < 45 | Functional but shallow — verify understanding |
| **Contender** | ≥ 64 | — | Interview recommended |
| **Thinker** | Build fail | ≥ 75 | Brilliant mind, broken build — worth a conversation |
| **Rookie** | ≥ 44 | — | Additional verification needed |
| **Incomplete** | Below thresholds | — | Below threshold |

**Hustler** is the most interesting rank. These candidates ship code that works perfectly — all tests pass, concurrency handled, edge cases covered. But the depth score is low: prompts show shallow engagement, design documents are thin, git history suggests copy-paste-and-move-on. The functional output is excellent, but the evidence of *understanding* is weak. This is the AI over-dependency signal. Part 3 of this series will explore what happened when we interviewed them.

**Thinker** is the most poignant. One candidate received this rank: their Docker build failed due to an environment mismatch, so their Base score was 0. But their code design, requirement derivation, and prompt quality scored in the top quartile. A purely functional evaluation would have rejected them outright. Our system said: this person thinks exceptionally well but stumbled on DevOps. That's worth an interview.

### The Distribution

| Rank | Count | % |
|------|-------|---|
| Ace | 64 | 17.5% |
| Craftsman | 154 | 42.1% |
| Hustler | 18 | 4.9% |
| Contender | 54 | 14.8% |
| Thinker | 1 | 0.3% |
| Rookie | 26 | 7.1% |
| Incomplete | 49 | 13.4% |

Hire recommended (Ace + Craftsman): **218 candidates (59.6%)**. Interview recommended (Hustler + Contender + Thinker): **73 candidates (19.9%)**. Below threshold: **75 (20.5%)**.

The 59.6% hire-recommend rate was higher than expected. It told us something about the candidate pool — or about our Depth threshold being too easy for AI-assisted submissions. We adjusted. Calibration never ends.

## Trusting the Machine

AI evaluating AI-assisted code creates an obvious trust problem. How do you know the AI grader isn't hallucinating scores? How do you know it's not biased by the order of evaluation? How do you know the same submission would get the same score twice?

Our answer: you don't trust a single layer. You build three.

{{< mermaid title="3-Layer Trust Architecture" >}}
graph LR
    A["Layer 1<br/>AI Evaluation<br/>(Stage 2 + 3)"] --> B["Layer 2<br/>AI Justification<br/>(Cross-verification)"]
    B --> C["Layer 3<br/>Human Adjustment<br/>(Delta ±10)"]
    C --> D["Final Score"]

    B -->|"overscored /<br/>underscored flags"| C

    style A fill:#2563eb,stroke:#333,color:#fff
    style B fill:#7c3aed,stroke:#333,color:#fff
    style C fill:#16a34a,stroke:#333,color:#fff
{{< /mermaid >}}

**Layer 1 — AI Evaluation.** The primary scoring pass. Stage 2 reads code and scores quality. Stage 3 runs Docker tests and scores function. Every score requires evidence.

**Layer 2 — AI Justification.** A separate agent re-reads the candidate's code and cross-checks the Layer 1 scores. Did Stage 2 say "error handling: 2 points — uses @ControllerAdvice"? The justification agent checks: does `@ControllerAdvice` actually exist in the code? If not: `overscored` flag. Did Stage 3 give 0 points on a test case due to build failure? The justification agent traces the code path: Controller → Service → Repository. If the logic is actually correct and only the build is broken: `underscored` flag.

**Layer 3 — Human Adjustment.** Where the justification layer flags discrepancies, a human reviews and applies corrections: −10 to +10 points per item, with structured reasoning recorded for audit trails.

The key insight from calibration data: AI consistently overscored certain categories — particularly "additional implementations" (business extensions like caching and monitoring) where the AI was generous with credit for features that existed but weren't well-integrated. Identifying these systematic biases let us add calibration hints to the scoring instructions, reducing variance in subsequent runs.

## Running the Machine

The numbers tell the operational story:

| Metric | Value |
|--------|-------|
| Total commits | 1,952 |
| Development period | 36 days |
| Evaluation commits | 1,162 (59.5%) |
| Candidates evaluated | 366 |
| Scoring model changes | 17 |
| Result resets | 26 |
| Automation workflows | 20+ |
| JSON schemas | 7 |
| Pipeline instruction files | 26 (11 skills + 15 commands) |
| Contributors | 3 |

The most revealing number: **system-building commits (feat/fix/refactor) were 14.5% of the total. System-operating commits (evaluation runs) were 59.5%.** Building the machine took 1 unit of effort. Running it took 4.

Five machines ran evaluations in parallel, each processing candidates sequentially to avoid context contamination — where the AI's judgment on candidate N is influenced by having just reviewed candidates 1 through N−1. Each candidate got a fresh, isolated agent session. Token limits required rotating across 10 accounts, swapping when usage approached thresholds.

An automation hub (built on n8n) coordinated everything beyond the AI evaluation itself: candidate roster management, distributed locking to prevent duplicate evaluations, progress dashboards, result aggregation, and notifications. This was the "iceberg below the waterline" — the AI scoring was visible, but the administrative automation underneath was just as substantial.

The original source was surprisingly mundane: Google Sheets. HR maintained the candidate roster there, and the automation pipeline synced it to the database, triggered GitHub invitations, collected submission URLs, and tracked evaluation state. The non-developers in the process never left their spreadsheets.

## What's Next

The pipeline tells you *who* to interview and gives you quantified signals. But a score and a rank aren't enough to walk into an interview room. You need to know *what to ask* each person — and the questions should be specific to their code, their design choices, their gaps.

That's where Part B of the machine begins: an AI system that reads each candidate's submission and generates a customized interview guide, with questions tied to specific `file:line` evidence, expected answers at different levels, and Socratic follow-up strategies. It's the bridge between the machine's judgment and the human's conversation.

In [Part 2-2, "Under the Hood,"](/posts/2026-02-24-ai-native-hiring-under-the-hood/) 이명훈 takes you inside the actual artifacts — scoring rubrics, test case ladders, endpoint detection across 366 different API designs, and the interview guide generation system. If this post is the architecture, that one is the implementation.

And in Part 3, "The Human," we'll explore what happened when interviewers sat across from candidates armed with these AI-generated guides — and why, unless you're hiring AIs directly, humans remain the final evaluators of humans.

---

*This is Part 2-1 of the AI-native hiring series.*
*Co-written with 이명훈.*
*Next: [**"Under the Hood: Rubrics, Pipelines, and AI-Curated Interview Guides"**](/posts/2026-02-24-ai-native-hiring-under-the-hood/) — the actual artifacts that powered the machine.*
