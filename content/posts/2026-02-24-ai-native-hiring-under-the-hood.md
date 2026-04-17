---
title: "Under the Hood: Rubrics, Pipelines, and AI-Curated Interview Guides"
date: 2026-02-24
draft: true
description: "The actual artifacts behind the machine: scoring rubrics with 8 dimensions, laddered test cases, endpoint detection across 366 different APIs, build rescue policies, and an AI system that generates customized interview guides with code evidence."
tags: ["ai", "hiring", "evaluation-pipeline", "automation", "interview-design"]
categories: ["Engineering"]
series: ["AI Native Hiring"]
ShowToc: true
TocOpen: false
---

> **⚠️ This post has been superseded.** A revised and consolidated version is available: [The Machine: AI Evaluating AI-Assisted Code](/posts/2026-04-14-ai-native-hiring-part2-the-machine/). This version is kept for reference.

> 🇰🇷 [한국어 버전 읽기](/posts/2026-02-24-ai-native-hiring-under-the-hood.ko/)
>
> This is Part 2-2 of the AI-native hiring series.
> [**Part 1**: "The Philosophy"](/posts/2026-02-24-ai-native-hiring-philosophy/) | [**Part 2-1**: "The Machine"](/posts/2026-02-24-ai-native-hiring-machine/) | **Part 3**: "The Human" (coming soon)

Written by [이명훈](https://medium.com/@myunghoon.lee). Introduction by Tao Kim.

---

In [Part 2-1](/posts/2026-02-24-ai-native-hiring-machine/), we walked through the architecture: the 7-stage pipeline, the 3-Tier scoring model, the reliability loop. That was the blueprint. This post is what's inside the walls.

이명훈 built and operated the evaluation pipeline from day one — the 1,952 commits, the 17 scoring model revisions, the late-night Docker debugging sessions when candidate #247's Gradle wrapper decided to download the internet. What follows are the actual artifacts: the scoring rubrics that defined *what* we measured, the test cases that defined *how* we measured, and the interview guide system that bridged the machine's output to the human's conversation.

---

# Part A: The Evaluation Pipeline

## Scoring Rubric: 8 Dimensions of Depth

Stage 2 — where the AI reads the candidate's code — scores across 8 dimensions, totaling approximately 120 points. These weren't arbitrary categories. They map to the philosophy from Part 1: in the AI era, *what you ask the AI to do* matters as much as *what the AI produces*.

| Area | Points | What it evaluates |
|------|--------|-------------------|
| **Prompts** | 18 | Depth of thinking in AI collaboration — root cause exploration, trade-off awareness, edge case surfacing |
| **Agent Instructions** | 17 | How well the candidate communicated project context to their AI (AGENTS.md, CLAUDE.md, or equivalent) |
| **Requirement Derivation** | 18 | Did they restructure the deliberately vague problem into explicit requirements? Concurrency analysis, lock design rationale |
| **Data Design** | 17 | Schema normalization, realistic seed data quality, batch insert strategy, DB constraints and indexing |
| **Code Quality** | 19 | Architecture layering, error handling, response consistency, dependency management, transaction handling |
| **Test Quality** | 10 | Unit, integration, E2E, and concurrency test coverage |
| **Git History** | 5 | Meaningful commit boundaries, descriptive messages |
| **Additional Implementation** | 10 | Beyond-spec work: caching, monitoring, API docs, performance optimization |

The first two areas — **Prompts** and **Agent Instructions** — are unique to AI-native evaluation. Traditional code reviews don't look at how the engineer communicated with their tools. But when the tool is an AI agent, the quality of that communication *is* part of the engineering.

### Evidence, Not Vibes

Every score requires evidence. The AI evaluator can't say "error handling is good — 2 points." It must say:

> `src/handler/GlobalExceptionHandler.java:15` — `@ControllerAdvice`-based global exception handler with domain-specific error codes mapped in `ErrorCode.java:5-40`.

This evidence requirement serves two purposes. First, it forces the AI to ground its scores in specific code rather than generating plausible-sounding assessments. Second, it creates an audit trail for the justification layer (Layer 2 from Part 2-1) to cross-check.

The rubric defines what evidence looks like for each scoring item. For the "Prompts" area, the evaluation checks for specific signals:

```yaml
# Example from the scoring rubric
prompts:
  insight_depth:
    keywords: ["왜", "트레이드오프", "예외", "만약", "edge case"]
    check: "Does the candidate explore WHY, not just HOW?"
    evidence_type: quote  # Direct quotes from prompt files
  ambiguity_exploration:
    check: "Did the candidate identify gaps in the problem statement?"
    evidence_type: file_line  # Reference to specific prompt file and line
```

Early in development, we discovered that without this checklist-based approach, the AI's scores varied by ±6-11 points across sessions for the same candidate. With evidence checklists, variance dropped to ±3.

## Test Case Design: Laddered Integer Scoring

Stage 3 runs 25 test cases across 8 categories. Every test case uses **laddered integer scoring** — not pass/fail, but a named spectrum of achievement levels.

Here's what a real test case definition looks like:

### TC-B1: Duplicate Enrollment Prevention (5 points)

> Same student attempts to enroll in the same course 5 consecutive times.

| Level | Points | What it means |
|-------|--------|---------------|
| 0 | 0 | No duplicate check — all 5 enrollments succeed |
| 1 | 1 | Inconsistent — duplicates sometimes blocked, sometimes not |
| 2 | 2 | Consistent 4xx error on duplicates |
| 3 | 3 | 4xx with meaningful error message (not generic 500) |
| 4 | 4 | 409 Conflict with idempotent handling |
| 5 | 5 | 409 + error response includes reference to original enrollment |

Level 5 is rare. Most production APIs don't return a reference to the original resource on conflict. But candidates who do show an awareness of API design beyond the minimum.

### TC-A1: Concurrency Race — 1 Seat Remaining (10 points)

> Pre-fill course enrollment to leave exactly 1 seat. Send 100 concurrent requests. Repeat 3 times for statistical reliability.

| Level | Points | What it means |
|-------|--------|---------------|
| 0 | 0 | No concurrency control — many succeed |
| 1 | 2 | Partial control (2-9 succeed consistently) |
| 2 | 4 | 1-2 succeed consistently (controlled but not exact) |
| 3 | 6 | 2 of 3 runs: exactly 1 success |
| 4 | 8 | 3 of 3 runs: exactly 1 success |
| 5 | 10 | 3 of 3 exact + clear error responses for failed attempts |

The 3-run repetition is critical. Concurrency bugs are probabilistic. A single run might pass by luck. Three runs surface non-deterministic failures.

The concurrency category alone is worth 22 points across 3 test cases (TC-A1 through TC-A3), making it the heaviest-weighted category. This reflects the philosophy from Part 1: the concurrency requirement was the deepest layer of the deliberately ambiguous problem.

### Scoring Distribution Across Categories

```
Core Function     ████████░░  10 pts   (CRUD basics)
Business Rules    ██████████████████  18 pts   (credit limit, conflicts)
Concurrency       ██████████████████████  22 pts   (the hard part)
Performance       ████████░░  10 pts   (response time, throughput)
Data Quality      ████████████  12 pts   (seed data realism)
API Design        ██████████████  14 pts   (REST conventions)
Edge Cases        ████████  8 pts    (boundary conditions)
Setup (Tier 1)    ██████  6 pts    (build + health check)
                             ───────
                             100 pts total (Base)
```

## The Endpoint Detection Problem

Here's a problem we didn't anticipate: **366 candidates means 366 different API designs.**

The same "course enrollment" function might be:

```
Candidate A: POST /enrollments           {"studentId": 1, "courseId": 5}
Candidate B: POST /api/v1/enrollments     {"student_id": 1, "course_id": 5}
Candidate C: POST /courses/5/enroll       (X-Student-Id: 1 header)
Candidate D: POST /registrations          {"studentId": 1, "courseId": 5}
```

Our test cases can't hard-code endpoints. So we built a 3-phase endpoint detection system:

**Phase 1 — Static analysis.** Grep the source code for framework-specific routing patterns:

```
Spring Boot:  @PostMapping, @GetMapping, @RequestMapping
Express:      app.post('/...'), router.get('/...')
FastAPI:      @app.post('/...'), APIRouter(prefix='/...')
NestJS:       @Controller('...'), @Post()
```

Class-level prefixes and method-level paths are combined to construct full paths.

**Phase 2 — Runtime verification.** After Docker startup, send probe requests to every detected endpoint. Some endpoints are declared in code but don't actually respond (incomplete implementations, disabled routes). This phase filters them.

**Phase 3 — Functional mapping.** Map verified endpoints to test case functions:

```
Enrollment:  /enrollments OR /registrations OR /courses/{id}/enroll → whichever responds
Cancellation: /enrollments/{id} OR /enrollments/{id}/cancel → whichever responds
Timetable:   /students/{id}/schedule OR /students/{id}/timetable → whichever responds
```

We also had to handle async APIs automatically. Some candidates implemented enrollment as an asynchronous operation — the POST returns a `jobId` or `PENDING` status, and you poll for the result. When the evaluator detects this pattern on the first enrollment test, all subsequent test cases switch to polling mode.

## Build Rescue and Graceful Degradation

About 15% of submissions failed to build. But "build failed" isn't one thing — it's a spectrum:

| Failure type | Partial credit | Example |
|-------------|---------------|---------|
| **Environment issue** | 28 pts | Gradle version mismatch, npm dependency conflict, DB connection string |
| **Compile error** | 14 pts | Syntax error, missing import, type mismatch |
| **Missing core** | 0 pts | Empty project, no controllers, no source code |

The classification uses pattern matching across language ecosystems:

```yaml
# From evaluation.yaml
environment_issue:
  java: ["Could not resolve all dependencies", "incompatible version"]
  node: ["npm ERR!", "Cannot find module", "ECONNREFUSED"]
  python: ["ModuleNotFoundError", "ConnectionRefusedError"]

compile_error:
  java: ["cannot find symbol", "incompatible types"]
  node: ["SyntaxError", "TypeError"]
  python: ["SyntaxError", "IndentationError"]
```

"npm version mismatch" and "empty project" are different stories. The first might work fine with a single version bump. The second has nothing to evaluate.

We also introduced **build remediation** — attempts to fix infrastructure-only issues without touching candidate source code. The policy was tiered by depth score:

- Depth ≥ 75: up to 5 remediation attempts (adjust DB versions, base images, environment variables, wrapper permissions, try alternative Dockerfiles)
- Depth ≥ 60: up to 3 attempts
- Depth < 60: 1 standard attempt only

The logic: if the AI code review says the candidate understands what they're doing (high Depth), it's worth extra effort to get their build working. If the code review says the understanding is shallow, a broken build is probably not just a DevOps issue.

Every remediated build is transparently flagged in the report. And remediated builds are capped at Level 1 on TC-01 (Docker Build) — they never get full marks for a build that needed help.

## Markdown as Pipeline

The entire evaluation pipeline is defined in markdown instruction files. There's no compiled evaluation application. The AI agent reads these documents and executes them.

A simplified view of how the evaluation command works:

```
evaluate-candidate (orchestrator)
│
├── Load: init/SKILL.md
│   └── Git clone, deadline check, checkout
│
├── Load: stage0/SKILL.md
│   └── Security scan (shell script execution)
│
├── Load: stage1/SKILL.md
│   └── Format validation
│
├── Load: stage2/SKILL.md
│   └── Read all code → Score 8 areas → JSON output
│
├── Load: stage3/SKILL.md
│   └── Detect language → Build Docker → Detect endpoints
│   └── Run 25 TCs → Ladder scoring → JSON output
│
├── Load: scoring/SKILL.md
│   └── 3-Tier calculation → Rank assignment
│
└── Load: report/SKILL.md
    └── Generate report → Update dashboard → Notify → Cleanup
```

For batch processing, a higher-level instruction file orchestrates the loop:

```
FOR candidate_id IN unprocessed:
    IF result already exists: SKIP
    TRY:
        Launch isolated agent session
        Execute: evaluate-candidate {candidate_id}
        Record: success
    CATCH:
        Record: error, continue to next
```

This isn't Python or shell. It's pseudocode that the AI agent reads and interprets. The control flow is explicit (`FOR`, `IF`, `TRY/CATCH`), but the syntax is flexible — what matters is *intent*, not *grammar*.

Sequential vs. parallel execution follows a safety-based rule, not a performance one:

| Operation | Concurrency | Why |
|-----------|------------|-----|
| AI code review | Sequential (1) | Context isolation required — prevent scoring bias |
| Docker testing | Sequential (1) | Port and memory contention across containers |
| Score calculation | Parallel (5) | JSON read/write only — no resource conflicts |
| Score justification | Parallel (3) | AI-intensive but fully independent per candidate |

## JSON Schema as Contract

AI agents are creative. Too creative, sometimes. Without constraints, the evaluation agent might output:

```json
{
  "score": 85,
  "rank": "Ace",
  "memo": "이 후보자 정말 잘함",
  "vibes": "excellent"
}
```

Helpful, but useless for downstream processing.

Seven JSON schemas enforce structural contracts on every stage's output. The agent generates JSON, the system validates it against the schema, and if validation fails, the error message is fed back to the agent for self-correction — up to 5 retries.

```
Agent generates JSON
  → Schema validation
  → FAIL: "score field exceeds max range; missing 'evidence' array"
  → Agent reads error, self-corrects
  → Re-validate
  → PASS (or fail again, up to 5 attempts)
```

Strict mode means no extra fields. If the schema defines `score` and `rank`, the agent can't sneak in `memo` or `vibes`. This sounds trivial, but at 366 candidates, every structural inconsistency becomes a downstream bug in statistics, CSV exports, and dashboard visualizations.

The Stage 3 schema alone validates: exactly 25 test cases with regex-validated IDs (`TC-01` through `TC-E2`), 8 required categories, level objects with `achieved/max/description` fields, and optional build failure classification with an enum of partial credit values.

---

# Part B: AI-Curated Interview Guides

The evaluation pipeline scored 366 candidates and identified who was worth interviewing. 108 advanced to the final round. Each one needed a customized interview guide — not generic technical questions, but questions tied to *their specific code*, *their specific gaps*, *their specific strengths*.

## Deep Insight: Per-Candidate Interview Guides

The Deep Insight system reads everything the pipeline produced about a candidate — source code repository, Stage 2 quality scores, Stage 3 functional results, and even their 1st round online coding test answers — then generates a customized interview guide.

Here's what a generated guide contains:

**1. Candidate profile.** Rank, scores, cohort percentile position. Where they stand relative to 366 candidates overall and the 108 in the interview pool.

**2. Part A — Technical verification questions (3-5 questions).** Each question is tied to specific code evidence:

> **Question 2: Dual-Lock Strategy and Deadlock Awareness**
>
> *Code evidence:* `EnrollmentService.java:30-59` — The candidate uses two `ReentrantLock` instances (`courseLock` and `studentLock`) to serialize concurrent enrollment.
>
> ```java
> // From the candidate's actual submission
> private final Map<Long, ReentrantLock> courseLocks = new ConcurrentHashMap<>();
> private final Map<Long, ReentrantLock> studentLocks = new ConcurrentHashMap<>();
> ```
>
> *Question:* "I see you're using two separate lock maps. Walk me through your thinking — why two locks instead of one? What happens if two students try to enroll in each other's courses simultaneously?"
>
> *Expected answer (5 pts):* Explains course-level lock protects capacity, student-level lock protects per-student constraints (credit limit, time conflicts). Describes lock ordering strategy to prevent deadlock.
>
> *Expected answer (3 pts):* Understands the two lock scopes but hasn't considered deadlock ordering.
>
> *Expected answer (1 pt):* Can describe what the code does but can't explain *why* two locks are necessary.
>
> *Follow-up:* "If you were deploying this across multiple server instances, would this locking strategy still work?"

**3. Abuse verification.** Questions from the 1st round online coding test, asked again in person. If the candidate can't explain their own prior submission, that's a flag.

**4. Part B — Values questions.** MUSINSA WAY behavioral questions, adapted to the candidate's profile. Even these can be connected to code evidence — for instance, asking about "Learning Agility" by referencing the candidate's prompt history showing they learned about locks from scratch during the assignment.

The design principle for question generation: **never directly mention the gap.** If the candidate's code has a bug in schedule conflict detection (e.g., using `equals()` instead of proper time overlap logic), the question doesn't say "your conflict check is wrong." Instead, it's Socratic: "If two courses have schedules Monday 09:00-10:30 and Monday 10:00-11:30, would your system detect the overlap? Walk me through the code path."

The interviewer knows the expected answer. The candidate discovers the gap — or reveals they already understand it.

## The S2 × S3 Matrix: Interview Strategy by Score Profile

Not all candidates need the same interview. The interview strategy varies based on where they sit in the Stage 2 (Depth) × Stage 3 (Base) grid:

{{< mermaid title="Interview Strategy by Score Profile" >}}
quadrantChart
    title S2 (Depth) × S3 (Base) Interview Strategy
    x-axis "Low Base (Function)" --> "High Base (Function)"
    y-axis "Low Depth (Understanding)" --> "High Depth (Understanding)"
    quadrant-1 "Ace / Craftsman: Push boundaries"
    quadrant-2 "Thinker: Debug skills, DevOps"
    quadrant-3 "Incomplete: Basic verification"
    quadrant-4 "Hustler: Probe understanding"
{{< /mermaid >}}

- **High Base + High Depth (Ace/Craftsman):** Push toward architectural boundaries. Ask about distributed systems, scalability trade-offs, what they'd change with more time.
- **High Base + Low Depth (Hustler):** The AI dependency check. "Walk me through this function line by line." "Why did you choose this lock mechanism?" Functional code + shallow understanding = potential over-reliance on AI generation.
- **Low Base + High Depth (Thinker):** Focus on debugging and environment skills. The thinking is there; the execution stumbled. Can they diagnose their own build failure? Given a hint about the issue, how quickly do they recover?
- **Low Base + Low Depth:** Basic verification. Is there growth potential? What did they learn from the experience?

16 of the 108 candidates were flagged for "special management" — cases where the S2/S3 profile was unusual enough to require extra interviewer preparation.

## Behavioral Anchors and the Hint Recovery Rule

Interviewers score on a 5-point scale with behavioral anchors calibrated for junior candidates:

| Score | Level | What it looks like |
|-------|-------|-------------------|
| 5 | Outstanding | Explains concepts the interviewer didn't ask about. Makes connections to broader systems. |
| 4 | Strong | Answers accurately, shows understanding of trade-offs, can extend to hypothetical scenarios. |
| 3 | Adequate | Core understanding is solid but doesn't go beyond what was directly implemented. |
| 2 | Below | Partial understanding. Can describe *what* the code does but not *why*. |
| 1 | Insufficient | Cannot explain their own implementation. |

The **hint recovery rule** adds nuance: interviews aren't exams. If a candidate initially misses a point but recovers after a nudge:

- **No hint needed:** Full score
- **1 hint → correct expansion:** −0.5 points
- **2+ hints → eventually correct:** One level down
- **Hints don't help:** Score stays where it is

This prevents penalizing candidates who are nervous but competent. The interview is a conversation, not a gotcha.

Part A covers three dimensions: Architecture Understanding (can they explain their system's structure?), Technical Depth (can they discuss concurrency, edge cases, trade-offs?), and Abuse Verification (did they actually write this code?).

Part B covers MUSINSA WAY values using the SBI method (Situation-Behavior-Impact), adapted for juniors. The key adaptation: we assess "intensity of experience" rather than "scale of experience." A junior who led a 3-person project with clear ownership demonstrates the same signal as a senior who led a 30-person initiative — at a different scale, but with the same behavioral markers.

### The Recommendation Matrix

```
Strong Recommend: Part A avg ≥ 4.0 AND Part B core avg ≥ 4.0
Recommend:        Part A avg ≥ 3.5 AND Part B core avg ≥ 3.5
Hold:             Either average between 2.5-3.5
Not Recommend:    Either average < 2.5
```

## The Scorecard Pipeline

Interviewers fill out markdown scorecards — structured documents with checklists, 5-point scales, and evidence blocks for each dimension. Here's a simplified example of what a completed scorecard looks like:

```markdown
## A-1. Architecture Understanding (1-5)

Checklist:
- [x] Can explain overall system architecture verbally
- [x] Understands the role of each layer (Controller/Service/Repository)
- [ ] Can discuss why they chose this architecture over alternatives
- [x] Identifies areas for improvement in their own design

Score: 3.5

Evidence:
> "이중 락 구조와 에러 처리 체계를 설명할 수 있었으나,
> 다른 아키텍처 대안에 대한 비교는 깊지 않았음."
```

These scorecards are parsed automatically: markdown → JSON → CSV. But the interesting part is what happens *after* parsing:

**Calibration analysis.** With 5 batches of interviewers assessing 108 candidates, interviewer bias is inevitable. We run OLS regression on interview scores versus pre-interview scores (Stage 2 + Stage 3) to detect systematic leniency or strictness per interviewer. One interviewer might average +0.19 above the expected line; another might average −0.15 below. These effects are computed and made available (transparently) to the hiring committee.

**Temperature analysis.** A separate check for tone-score consistency. Does the written evidence in the scorecard match the numerical score? If an interviewer writes enthusiastic notes ("탁월한 이해도, 깊은 사고력") but scores 3/5, or writes critical notes but scores 4/5, the system flags the discrepancy for review.

**The 3-layer debrief stack.** The final hiring committee sees three layers:

1. **Raw scores** — what the interviewer reported
2. **Temperature-adjusted** — corrected for tone-score mismatches
3. **Calibration-adjusted** — corrected for interviewer bias

No individual layer overrides the interviewer's judgment. But the combination gives the committee a more complete picture than raw scores alone.

---

## Closing

What's assembled across Parts 2-1 and 2-2 is a system with three interlocking components: **evaluation pipeline** (AI reads code, machine runs code), **scoring model** (3-Tier with laddered test cases), and **interview preparation** (AI generates per-candidate guides from code evidence).

The system processed 366 candidates, generated 108 interview guides, and supported 5 batches of interviewers — with calibration and quality control at every step.

But the machine's output is ultimately an input to a human decision. In Part 3, "The Human," we'll explore what happened on the other side of that decision — in the interview rooms where interviewers sat across from candidates, armed with AI-generated guides, and had to answer the question that no machine can: *is this someone we want to build with?*

Unless we're hiring AIs directly, that question belongs to humans.

---

*This is Part 2-2 of the AI-native hiring series.*
*Written by 이명훈.*
*Next: **"The Human: Why Humans Are Still Required"** — what happened when AI-generated interview guides met real conversations.*
