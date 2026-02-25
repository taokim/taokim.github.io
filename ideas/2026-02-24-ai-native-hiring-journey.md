---
title: "AI Native Hiring (3-Part Series)"
series_titles:
  part1: "The Philosophy: AI-Native Hiring"
  part2: "The Machine: AI Evaluating AI-Assisted Code"
  part3: "The Human: AI-Curated Interviews"
date: 2026-02-24
tags: [ai, hiring, automation, engineering-culture, claude-code, problem-design]
status: ready
priority: high
category_hint: "Engineering"
language_hint: bilingual
related: ["2026-02-24-stamp-carver-vs-factory-builder"]
refined_date: 2026-02-24
---

## Raw Dump

planning to post about AI native hiring journey. From initial thought, details on the decisions, how-to, automated process for hiring from scoring, verifying, and even offline interview guideline which were curated for the candidate. There're bunch of documents and codes/documents for AI processing. ~/ws/mukie-ai-evalutor => AI evaluator for AI native coding test. ~/ws/mukie-ai-evaluator-v2 => AI helper for 3rd offline interviews. /Users/musinsa/Downloads/무신사 루키즈 3차 인터뷰 가이드라인.md => general interview guideline for the offline interviewers, /Users/musinsa/Downloads/무신사 루키 채용 AI 코딩 테스트 전략안.pdf => initial idea doc. Read these docs and dump the idea (with organized version)

## Raw Dump (Refinement #1)

Think about the complexity of the pipeline. Not just reading the codes, we need to run the service to verify it is really working. How to do it? Just asking dockerize? It is too simple. The candidates or engineer should think about how to test it not just for me but for who will use my code later for any purpose. Just like that, I'd tried to make things vague, and asked to think deeply in different perspective, not just for themselves, but for who is reading and running their codes. To achieve this, the problem or the quiz was very unclear. Let's get the problem itself and explain the intention of it. It is starting from the short memo of service or ops team but without any NFR or clear req. It is not asking specific lock mech nor perf req, nor constraints. The candidate should think about it is impossible one student make the registration on same time with multiple courses. API might be easier for this problem, how about data generation? When seeding the data, the candidate should think about this complexity. If the candidate built the feature with unique index, the validate might work in infra level. If they generated data directly from code with code-level protection, data generation would be broken. In AI era, what to do is most important. AI native engineers should be able to think about the problem statement deeply, rather than jumping into the solution.

## Raw Dump (Refinement #2)

First title, "Philosphy" looks good. "Philosophy - AI Native Enginner" as a full title? I'm thinking about the problem or difficulty of typical leet-code style coding test or algorithm test since it is too easy with agent abusing. So, asking to code in constrainted env looks totally wrong. Innovative or breaking through idea required on this domain. Allow everything and just ask what to do as aligned with our "philosophy". So that, we should be able to evaluate huge output of the agents that is very hard to evaluate in old way, typical test cases. If you want to have specific API endpoint or UI for easy testing, that is actually huge hints for the implementation. Full vaguness is hard problem still to AI agent since they don't know exactly the intention of the quiz, but half vaguness, it is another round, too easy to fill half empty box with the inferences. In time perspective to solve the problem, 1h is slightly short since the candiates need to build all env to make the app launch. Short algorithm code in C or C++ is not enough for the testing, that's 1 min problem today. But, asking to build full service or app requires more time but this is irony, this is making hard to test, then we might need to ask to dockerize... But, as already mentioned, that's too big hints. So, I deicded to let them do whatever they do, but asked to do similar way of open source doing, document how to run or test. The agents can run them and test their features. With this philosphy on the problem denfition combined with how to solve it with the agent, we could set new interview mechanism to hire AI native engineers.

## Raw Dump (Refinement #3)

Think about "AI Native", not just hiring but defitinion perspective, what is it? Just like old Internet era or Smart-Phone era, there are ppl who is grown with that, who are not used to before-era. In Japan, heard about the case the comapnies educating new hires how to use keyboard. Think about making them not used to old system but utilizing them as much as possible of their nativeness for AI. I believe they used to use AI than my generation. (Let's do not make any judgement that's right or not, we cannot avoid to use AI, esp. in coding domain) So that, let's define them and emphasize their nativeness on AI. DO NOT avoid hiring in this chaotic era esp. many companies don't how to deal with this situation, but Musinsa is different. We're passing through this difficulty with the bold decision and believe or even theory. There is a generation who used to AI or grown with AI, let's emphasize their strength and break through the fearness of huge wave that no one knows what eventaully happen. Just find the poisition in the frontline and move forward as a pioneer.

## Raw Dump (Refinement #4)

more clues and examples,
- Andrew Ng's mention in Standford CS class, PM:Eng ratio was 1:6 or 1:8, but these days in bleeding edge, 2:1, implmentation is cheapest thing in this domain
- Vaguness example, race condition, didn't ask specific level algo or one machine vs distributed system. Some candidate just use application-level lock in synchronized feature or in-memory fifo queue, some are finding the option with database pessimistic lock, some are thinking about some custom optimistic with the intended naiveness, some ppl can eventually reach on the distrubued lock with Redis. But, not many ppl think about the distributed transaction problem in deeply. Maybe, they can be top scorer.
- Business logic example. I'm not asking to get a lock for Course or Student. If they decided to aquire the lock only for Course, it could be ok in simple example. But, they will realize any correct implementation must serialize at the student level because the per-student invariants require it.
  The mechanism can differ — pessimistic lock, optimistic lock + retry, application-level lock, distributed lock (Redis/ZooKeeper),
  or even serializable isolation — but the requirement is the same: concurrent requests from the same student must be serialized to
  prevent invariant violations.

  Course-level serialization alone only protects the capacity invariant (shared resource across students). Student-level
  serialization protects per-student constraints. You need both.

## Source Materials

### 1. Initial Strategy Doc (PDF)

- **What**: "[무신사 루키 채용] AI Native 개발자 채용을 위한 기술적 접근 및 협업 제안"
- **Key thesis**: "AI를 얼마나 잘 활용하는가"가 곧 개발 실력인 시대 — hiring must test AI collaboration, not just raw coding
- **The dilemma**: Local environment freedom = fairness problem (tool gap becomes skill gap)
- **Why existing tools fail**: HackerRank-style sandboxes can't evaluate "AI-driven development" — limited AI assistants, no real environment, can't control external tool usage
- **3 options explored**: (A) Platform collaboration / API credits, (B) Standardized Musinsa dev environment, (C) Fallback to existing solutions
- **Vision**: Not just a one-time system but R&D toward defining "AI Native Engineer" standards, expandable to all hiring and internal assessment

### 2. AI Evaluator v1 (`~/ws/mukie-ai-evaluator`)

- **What**: Fully automated AI coding challenge evaluation agent
- **Pipeline**: `/project:init` → `stage0` (security scan) → `stage1` (format validation) → `stage2` (quality eval by AI, 120pts across 8 dimensions) → `stage3` (functional eval via Docker, 100pts across 25 TCs) → `scoring` → `report`
- **Scale**: ~360 candidates evaluated, ~1800 → ~400 after 1st round screening
- **Scoring model**: 3-Tier (Make it Work → Basic Features → Deep Thought), ~220pts total
- **Key innovation**: AI evaluates AI-assisted code — prompts quality, agent instructions, requirement derivation, code quality, test quality, git history, additional implementations
- **Rank system**: Ace > Craftsman > Hustler > Contender > Thinker > Rookie > Incomplete
- **Infrastructure**: Docker containers per candidate, port isolation, n8n orchestration, PostgreSQL, Slack notifications, JSON schema validation loops
- **Problem**: "University course registration system" REST API — tests concurrency, business rules, performance, API design

### 3. AI Evaluator v2 (`~/ws/mukie-ai-evaluator-v2`)

- **What**: 3rd round (offline interview) preparation system powered by Claude Code
- **Purpose**: Generate per-candidate interview guides with code evidence, manage scorecards, aggregate results
- **Key feature — Deep Insight**: AI reads each candidate's submitted code + evaluation reports → generates customized interview questions with specific file:line code evidence, expected answer tiers (5→1 scale), follow-up questions
- **Interview structure**: Part A (30min, technical verification) + Part B (30min, MUSINSA WAY values — Learning Agility, Deliver Results, Collaborate Across Boundaries)
- **Scoring rubric**: Behavioral anchors (5-point scale), hint recovery rules, automatic recommendation logic
- **Automation**: Batch operations via Claude Code skills/commands, scorecard parsing (md→json), statistics dashboard, CSV export
- **Scale**: 108 candidates for 3rd round, 5 batches of interviewers

### 4. Offline Interview Guideline

- **What**: Comprehensive interviewer guide for 3rd round
- **Structure**: Part A (assignment verification, 30min) + Part B (MUSINSA WAY verification, 30min)
- **Rank-based question strategy**: Different depth for Ace/Craftsman vs Contender/Thinker vs Rookie
- **Abuse detection**: 1st round coding test questions asked again to verify authenticity
- **SBI method**: Situation-Behavior-Impact, adapted for junior candidates (focus on growth potential over scale of experience)
- **7 MUSINSA WAY values**: Customer & Brand Focus, Ownership, Learning Agility, Deliver Results, Raise the Bar, Simplify, Collaborate Across Boundaries

### 5. The Problem Statement (`problems/수강신청/PROBLEM.md`)

- **Format**: A casual memo from "기획팀" — deliberately informal, incomplete, mimicking real-world ops requests
- **Explicit requirements**: Student/course/professor listing, enrollment, cancellation, timetable, 18-credit cap, time conflict prevention, concurrency ("100명이 동시에 신청해도 정확히 1명만 성공")
- **Deliberately omitted**: Lock mechanism, performance requirements, specific constraints, NFRs, authentication, cancellation policies, prerequisite rules
- **Data generation requirements**: 10,000+ students, 500+ courses, dynamic generation (no static files), realistic data, within 1 minute — but no guidance on how to handle constraint conflicts during seeding
- **Key design philosophy**: "기획팀의 요청사항은 의도적으로 불완전합니다" — the ambiguity IS the test

## Structure (3-Part Series)

### Part 1: "The Philosophy: AI-Native Hiring"

**Central thesis**: There is a generation that grew up with AI — just as there were digital natives and mobile natives before them. Instead of fearing this shift, we chose to define what "AI native" means, build a theory around it, and hire for it. Here's the philosophy.

#### 1.1 What Is "AI Native"? (the manifesto)

- **The generational parallel**: "Digital native" (internet era), "mobile native" (smartphone era), now "AI native"
- These aren't people who *learned* to use AI — they grew up with it. AI is their natural environment.
- The Japan keyboard analogy: companies teaching smartphone-native new hires how to use keyboards. The lesson isn't "teach them the old way" — it's leverage their nativeness.
- Don't make them unlearn. Don't force them into pre-AI workflows. Recognize what they're naturally good at and build on it.
- No judgment on whether this is "right" — we cannot avoid AI in coding. Accept the reality and work with it.

#### 1.2 The Fear and the Freeze (industry context)

- Most companies are paralyzed. They don't know how to evaluate candidates who use AI.
- Some ban AI from interviews. Some pretend AI doesn't exist. Some freeze hiring entirely.
- The fear is real: "no one knows what eventually happens" with this wave.
- The result: a generation of talent is being undervalued or ignored because the industry can't figure out how to assess them.

#### 1.3 Musinsa's Choice: Pioneer, Not Bystander

- "DO NOT avoid hiring in this chaotic era"
- While others freeze, Musinsa chose to define "AI native engineer," build a theory, and test for it.
- This isn't recklessness — it's a calculated bet: the companies that figure out AI-native hiring first will attract the best of this generation.
- "Find the position in the frontline and move forward as a pioneer."
- The bold decision: build an entirely new evaluation pipeline from scratch, grounded in a philosophy about what engineering means now.

#### 1.4 LeetCode Is Dead

- "Short algorithm code in C or C++ is not enough for the testing, that's 1 min problem today."
- AI agents solve traditional algorithm problems trivially — LeetCode, HackerRank are measuring the wrong thing
- The constrained-environment response (web sandbox, no AI tools) is the wrong direction — "asking to code in constrained env looks totally wrong"
- You're testing who can code without their actual tools — like testing a carpenter without power tools. The skill has shifted.
- **Andrew Ng's signal**: In Stanford CS class, he mentioned PM:Eng ratio was 1:6 or 1:8. Now in bleeding-edge teams, it's 2:1. Implementation is the cheapest thing in this domain now. If implementation is cheap, what's expensive? *Knowing what to implement.*
- The industry needs a fundamentally new approach, not a patch on the old one

#### 1.5 The Fairness Problem

- Initial hypothesis: "환경 구축도 실력이다" — let candidates use their own tools
- Reality check: tool gap becomes skill gap. Claude Pro + optimized setup vs nothing = unfair competition on capital, not skill
- "We want to test problem-solving thinking, not who has more expensive tools"
- Decision: allow everything, but provide equal AI environment so the playing field is level

#### 1.6 The Vagueness Spectrum (key framework)

The calibration of problem design is everything:

- **Full vagueness**: Still hard for AI agents — they don't know the intention of the quiz. But also unproductive for candidates.
- **Half vagueness**: Too easy — "too easy to fill half empty box with the inferences." Specify API endpoints or UI? That's huge implementation hints. AI fills the gaps instantly.
- **The sweet spot**: Vague *requirements* + clear *outcome expectation*. The candidate must derive what to build. AI can execute but can't decide *what* to execute.

The problem statement is a memo from "기획팀" — deliberately casual, deliberately incomplete. No NFRs, no lock mechanisms, no performance requirements. "기획팀의 요청사항은 의도적으로 불완전합니다" — the ambiguity IS the test.

This is what separates "AI native" from "AI dependent": can you figure out *what to do* when the spec doesn't tell you?

#### 1.7 The Time/Scope Paradox and the Open Source Resolution

The reasoning chain:

1. Algorithm tests → 1 min with agents. Dead.
2. So test full service/app → but needs 2-3 hours (1h too short — env setup alone eats time)
3. Full service = hard to test at scale → need Docker? → but Docker requirement = big implementation hint
4. Specifying any test infrastructure = giving away the answer

**The resolution**: Don't specify infrastructure at all. Ask candidates to document like open source — "document how to run or test." Then *agents* read the docs and test their features. The candidate who thinks about testability-for-others naturally writes a better README, better build scripts, better health checks — without being told to.

This connects problem philosophy to evaluation method: the *way* they package their code IS part of what we evaluate.

#### 1.8 Designing Ambiguity: The Problem as a Mirror

- Walk through the actual problem: "대학교 수강신청 시스템" — a university course registration system
- It starts as a memo from ops/planning team: "매 학기 수강신청 기간마다 서버가 다운되어..."
- What's explicit: basic features, 18-credit cap, time conflicts, concurrency requirement
- What's deliberately missing: lock mechanisms, performance SLAs, constraints, NFRs, auth, cancellation policies
- The problem says: "명시되지 않은 모든 사항은 자유롭게 결정하세요" — decide everything that's not specified
- This tests requirement derivation under ambiguity — the core skill of an AI-native engineer

#### 1.9 Case Studies: Where the Traps Are

**The Data Generation Trap**:

- Requirement: 10,000+ students, 500+ courses, realistic, dynamic, within 1 minute
- If you implement DB-level constraints (unique index) → seeding works fine, DB handles integrity
- If you implement code-level validation (check before insert) → your own business rules block your seeder: time conflicts, credit limits, capacity limits
- The candidate who *thinks ahead* designs the seeding path to intentionally bypass or respect constraints — and documents why
- This reveals: "who will run this code next?" vs "does it work for me right now?"

**The Concurrency Ladder** (how vagueness reveals depth of thinking):

The problem says "100명이 동시에 신청해도 정확히 1명만 성공" but never specifies how. We didn't ask for a specific algorithm, didn't say single-machine vs distributed. What candidates chose reveals their depth:

| Level | Approach | What it reveals |
|-------|----------|-----------------|
| Surface | `synchronized` block / in-memory FIFO queue | Knows the word "concurrency" but thinks single-JVM only |
| Competent | DB pessimistic lock (`SELECT ... FOR UPDATE`) | Understands DB-level serialization, practical choice |
| Thoughtful | Custom optimistic locking with intentional retry | Understands trade-offs, chose performance over simplicity |
| Deep | Distributed lock (Redis/ZooKeeper) | Thinks beyond single machine, considers production reality |
| Exceptional | Distributed transaction problem analysis | Thinks about what happens when the lock *itself* fails — the top scorers |

No level is "wrong" — but the *reasoning* behind the choice is what we evaluate. A candidate who chooses `synchronized` and explains why (single-instance scope, simplicity for the given constraints) scores better than one who uses Redis locks without understanding why.

**The Dual-Lock Insight** (the deepest trap):

We didn't ask candidates to lock Course or Student specifically. Most candidates only think about course-level locking — protect the capacity invariant (shared resource across students). This works for the obvious case: 100 students → 1 seat.

But any *correct* implementation must also serialize at the student level. Why? Per-student invariants:
- 18-credit cap: two concurrent requests from the same student could both pass the credit check before either commits
- Time conflict: two concurrent requests could both pass the schedule check before either commits
- Duplicate enrollment: same student, same course, two concurrent requests

Course-level serialization protects: capacity (shared across students)
Student-level serialization protects: credit limit, time conflict, duplicate enrollment (per-student constraints)
**You need both.**

The mechanism can differ — pessimistic lock, optimistic lock + retry, application-level lock, distributed lock, or serializable isolation — but the *requirement* is the same: concurrent requests from the same student must be serialized to prevent invariant violations.

The candidates who discover this on their own — through thinking about the problem, not being told — are the ones we want to hire. This is "what to do" thinking. AI can implement any locking mechanism you ask for. But AI won't tell you that you need *two different serialization scopes* unless you think to ask.

#### 1.10 The 3-Tier Evaluation Philosophy

- Make it Work → Basic Features → Deep Thought
- Why "depth of thought" (120pts) outweighs "functional correctness" (100pts)
- Prompt quality > code quality in scoring weight — because in AI-native development, the prompt IS the engineering
- We need to evaluate huge output of AI agents — traditional test cases don't scale. New evaluation methods required.
- The rank system (Ace → Incomplete) and what each rank signals

**Tone**: Manifesto opening (define AI native, industry fear, pioneer stance), provocative middle (LeetCode is dead, vagueness spectrum, time paradox), concrete close (case studies, scoring). This is the post that makes hiring managers question their own process — and gives them a framework to act.

**Connection to existing work**: Reference `stamp-carver-vs-factory-builder` — "We wrote about how AI changes what we build; here's how it changes how we hire the builders."

### Part 2: "The Machine: AI Evaluating AI-Assisted Code"

**Central thesis**: We built an automated pipeline where AI evaluates AI-assisted code at scale. Here's how the machine works.

#### 2.1 Architecture Overview

- The full pipeline: clone → security scan → format validation → AI quality eval → Docker functional eval → scoring → report
- Why each stage exists and what it catches
- Scale: ~360 candidates, fully automated, commit-and-push results

#### 2.2 Stage 0-1: The Gates (Security + Format)

- Security scan: why it comes first (candidates submit arbitrary code that runs in Docker)
- Format validation: README, CLAUDE.md, prompts/, docs/REQUIREMENTS.md — if you can't organize your deliverables, that's a signal
- Pass/Fail — these are binary gates, not scored

#### 2.3 Stage 2: AI Evaluating Depth (120pts)

- 8 dimensions: prompts, agent instructions, requirement derivation, data design, code quality, test quality, git history, additional implementations
- The meta-layer: Claude evaluating how well candidates used AI (prompt quality, agent instructions)
- Evidence-based scoring: every point requires a file path, line number, or quote — no vibes-based grading
- Checklist-based approach to reduce session-to-session variance
- JSON schema validation loops (up to 5 retries) — ensuring evaluator output is machine-parseable

#### 2.4 Stage 3: Functional Testing via Docker (100pts)

- 25 test cases across 8 categories
- Docker build → health check → data exploration via list APIs → targeted testing
- The open-source paradigm in action: evaluator reads candidate's docs to understand how to test
- Concurrency tests: statistical reliability via 2-3 repeat runs per TC
- Ladder scoring: partial credit within each TC (not binary pass/fail)
- Build failure policy: even failed builds get partial scores based on failure type

#### 2.5 Scoring & Ranking

- 3-Tier model: Base(100) + Depth(~120) = ~220pts
- The rank system and what each rank signals about the candidate
- Strength profile: 5 dimensions (execution, business logic, concurrency, quality, depth)
- The "Hustler" problem: high base score but low depth — signals possible AI over-dependency

#### 2.6 Infrastructure & Scale

- Docker containers per candidate, port isolation for concurrent evaluation
- n8n orchestration, PostgreSQL for state management
- Slack notifications, automated commit-and-push
- Batch processing: evaluate-loop, machine.json-based parallelization
- How we processed ~360 candidates across multiple machines

**Tone**: Technical deep-dive. Architecture diagrams, scoring formulas, example outputs. The "how we built it" post for engineers who want to replicate something similar.

### Part 3: "The Human: AI-Curated Interviews"

**Central thesis**: Automation gets you to the shortlist. But detecting AI dependency, growth potential, and values alignment still needs humans — so we used AI to make humans better interviewers.

#### 3.1 Why We Still Need Humans

- The "Hustler" problem: candidates with perfect functional scores but shallow understanding
- AI dependency detection: can the candidate explain their own code?
- Values alignment: MUSINSA WAY can't be tested by a machine
- The 1차 abuse detection: asking original coding test questions again in person

#### 3.2 Deep Insight: AI-Generated Interview Guides

- How it works: AI reads candidate's code + evaluation reports → generates customized interview questions
- Every question tied to specific `file:line` code evidence
- Expected answer tiers (5→1 scale) with follow-up questions
- Rank-based question strategy: different depth expectations for Ace vs Contender vs Thinker
- The Socratic approach: guide candidates toward gaps rather than grilling

#### 3.3 Standardizing Human Judgment

- Behavioral anchors: 5-point rubric with concrete examples for each level
- Hint recovery rules: how to credit candidates who recover with a nudge
- SBI method adapted for juniors: focus on "intensity of experience" over "scale of experience"
- Automatic recommendation logic: strong hire / hire / hold / no hire

#### 3.4 The Scorecard System

- Structured markdown → JSON parsing → CSV aggregation → statistical dashboard
- Batch processing via Claude Code skills/commands
- How we maintained consistency across 5 batches of interviewers, 108 candidates

#### 3.5 Lessons & Surprises

- What worked better than expected
- What we'd change next time
- The meta-irony: using AI to evaluate AI-native developers, then using AI to prepare humans to interview them
- Scalability: from rookie hiring to company-wide engineering assessment standard
- Open questions: fairness, evolving AI capabilities, the moving target of "AI native"

**Tone**: Reflective, honest about trade-offs. The "what we learned" post that engineering leaders and hiring managers bookmark.

## Counter-Arguments to Address

- "Isn't deliberate ambiguity just bad problem writing?" → No — real engineering work never comes with complete specs. The ability to handle ambiguity is the skill we're testing.
- "How do you distinguish 'thoughtful simplification' from 'missed the point'?" → That's exactly what the requirement derivation scoring (12pts) measures — it rewards documented decisions, not just outcomes.
- "Does this approach disadvantage methodical-but-slow candidates?" → The 3-Tier model addresses this: "Make it Work" comes first. Partial submissions are explicitly welcomed and scored.
- "Is AI evaluating AI-assisted code reliable?" → Evidence-based scoring with checklists and schema validation reduces variance. But we still need humans for the final round — that's the whole point of Part 3.
- "Why not just ban AI tools and test 'real' skill?" → That's the constrained-environment fallacy. You're testing who can code without their actual tools — like testing a carpenter without power tools. The skill has shifted.
- "Isn't this too risky for a company to try?" → The bigger risk is doing nothing. Companies that freeze hiring or cling to old evaluation methods will miss an entire generation of talent.

## Notes

This is a high-priority, deeply substantive idea with real production artifacts (two codebases, strategy docs, interview frameworks). The "AI evaluating AI-assisted code" angle is genuinely novel and timely — few companies have documented this process publicly. The post could be a landmark piece: practical enough to be a how-to, philosophical enough to spark industry conversation about what hiring means in the AI era. Recommended as a multi-part long-form post (bilingual, separate files).

**Refinement #1 insight**: The central thesis should be "what to do > how to do it" in the AI era. The problem design philosophy (intentional ambiguity, data generation trap, concurrency paradox, dockerization-as-philosophy) is the gravitational center of the series, not just a section within it. Part 1 should be the philosophical anchor that makes readers rethink their own hiring processes.

**Refinement #2 insight**: Part 1 needs three new frameworks woven in: (1) The Vagueness Spectrum — full vagueness vs half vagueness vs the sweet spot, (2) The Time/Scope Paradox — algorithm=1min, full service=hard to test, resolved by open-source paradigm (document how to run, let agents test), (3) LeetCode Is Dead as the provocative hook. The series titles follow a rhythmic pattern: "The Philosophy:", "The Machine:", "The Human:".

**Refinement #3 insight**: Part 1 opens with a manifesto, not mechanics. "AI Native" is a generational concept — like digital native or mobile native. The post should define the term, acknowledge industry fear/paralysis, and position Musinsa as a pioneer making a bold bet. The emotional arc: define → acknowledge fear → choose courage → then show the method. New counter-argument: "Isn't this too risky?" → The bigger risk is doing nothing.

**Refinement #4 insight**: Added concrete examples that make the philosophy tangible. Andrew Ng's PM:Eng ratio (1:8 → 2:1) as authority for "implementation is cheap." The Concurrency Ladder showing how vagueness reveals thinking depth (synchronized → pessimistic → optimistic → distributed → distributed transaction). The Dual-Lock Insight as the deepest case study: course-level vs student-level serialization, why you need both, and why AI won't discover this for you.

**Series format**: 3 separate bilingual posts with cross-links. Each part stands alone but rewards reading in sequence. Part 1 is the manifesto + philosophy, Part 2 is the technical depth, Part 3 is the human payoff + lessons.

## Editorial Notes (EN-KO Mapping & Decisions)

These conventions apply across all 3 parts of this series. Established during Part 1 polish.

### Translation Mappings
| EN | KO | Note |
|---|---|---|
| product team | 기획팀 | EN uses "product team" (closer to Western org structure); KO keeps 기획팀 (natural Korean corporate term) |
| The Fear and the Freeze | 얼어붙은 업계 | EN is catchy; KO is descriptive |
| Pioneer, Not Bystander | 먼저 움직이기로 했다 | EN is label-style; KO is action-style |
| What We Actually Asked | 실제로 무엇을 물었나 | |
| Hidden Depths | 숨겨진 깊이 | |
| From Philosophy to Practice | 철학을 실전으로 | |
| The Data Design Trap | 데이터 설계 함정 | |
| The Concurrency Ladder | 동시성 사다리 | |
| The Dual-Lock Insight | 이중 락 인사이트 | |

### Tone Decisions
- **Duck typing metaphor**: Used in both languages. EN extends playfully ("does it quack?", "open the duck up"). KO references the concept ("덕 타이핑과 같은 원리다") but avoids 꽥꽥 applied to candidates — it sounds dismissive in Korean. Tier 3 is framed as going "beyond duck typing" (덕 타이핑 너머로) since duck typing = external behavior only, but Tier 3 inspects internals.
- **Andrew Ng / PM ratio**: Eng:PM from 7:1-8:1 → 2:1 or 1:1, plus role merger (product-minded engineer). EN embeds YouTube `AuZoDsNmG_s`, KO embeds `vENN6-d_3AQ` (Korean-dubbed version).
- **"People, not logos" quote**: Softly woven into the Pioneer section in both languages. Not attributed — presented as a recurring thought. Connects to Musinsa angle: optimizing for the right people growing together.
- **Score numbers**: Deliberately omitted (100pts, 120pts, 220pts, rank names). Philosophy is preserved without granular internals. Part 2 can go deeper if needed.
- **Humility / AI overriding theory**: NOT framed as "AI might make this easy someday" (reads as limitation). Instead framed as "arriving at the insight IS the skill, whether through own reasoning or AI collaboration" (duck typing). Then the scoring section adds nuance: delegation vs engagement (Anthropic research).
- **Anthropic research**: Shen & Tamkin (2026) + Anthropic internal data (Dec 2025). Lives in the scoring section (not the dual-lock section) because it structurally motivates WHY Tier 3 is weighted highest.
- **Counter-argument ("you told them what to build")**: Addressed directly in "What We Actually Asked" section. Response: a phrase is a starting point, not a spec. Candidates have lived this problem. Defining the problem IS the skill. This used to be senior IC territory — now every engineer needs to work this way.
- **Data trap**: Candidates were NOT asked to seed enrollment records — just students, professors, courses. Trap is about relationship design quality (schedules, capacities, professor loads), not business-rule blocking during seeding.
- **Freeze narrative**: About AI chaos broadly (do we hire? how many? juniors vs seniors?), NOT about difficulty of evaluation specifically.
