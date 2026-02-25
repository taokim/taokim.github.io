---
title: "The Philosophy: AI-Native Hiring"
date: 2026-02-24
draft: true
description: "LeetCode is dead. In the AI era, implementation is cheap — knowing what to implement is the real skill. Here's how we designed a hiring process to find AI-native engineers."
tags: ["ai", "hiring", "engineering-culture", "problem-design"]
categories: ["Engineering"]
series: ["AI Native Hiring"]
ShowToc: true
TocOpen: false
---

> 🇰🇷 [한국어 버전 읽기](/posts/2026-02-24-ai-native-hiring-philosophy-ko/)
>
> This is Part 1 of a 3-part series on AI-native hiring.
> **Part 2**: "The Machine" (coming soon) | **Part 3**: "The Human" (coming soon)

---

## What Is "AI Native"?

Every technology era produces its natives.

The internet era gave us "digital natives" — a generation that never knew life without Google. The smartphone era gave us "mobile natives" — people whose first instinct is to tap, not click. Now the AI era is producing its own generation: **AI natives**.

These aren't people who *learned* to use AI. They grew up with it. ChatGPT was there when they started college. Copilot was in their first IDE. Claude helped them debug their first production bug. AI isn't a tool they adopted — it's the air they breathe when they code.

I heard about companies in Japan that now teach new hires how to use a physical keyboard, because the incoming generation grew up on smartphones. The instinct of most organizations is to "fix" this — to train the new generation back to the old way. But the better instinct is to ask: *what are they naturally good at that we aren't?*

The same applies to AI. Instead of asking "how do we prevent candidates from using AI?", we should ask: **"how do we find the ones who are genuinely great at working with AI?"**

That question led to everything in this series.

## The Fear and the Freeze

The AI wave hasn't just changed how engineers work. It's thrown the entire talent equation into chaos.

Companies aren't just struggling with *how to evaluate* candidates — they're struggling with more fundamental questions. Do we even need to hire? How many engineers does an AI-augmented team actually need? Should we bring in fresh juniors who'll need years of mentoring, when AI might reshape the job before they're ramped up? Or do we double down on experienced seniors and hope they adapt?

The responses range from denial to paralysis:

- **Ban it**: "No AI tools allowed during the coding test." (You're testing who can code without their actual tools — like testing a carpenter without power tools.)
- **Ignore it**: Run the same LeetCode-style interviews and pretend nothing changed. (Candidates solve your algorithm problems in 60 seconds with an agent. You just don't see it.)
- **Freeze**: Stop hiring entirely until "things stabilize." (Things won't stabilize. The wave only accelerates.)

The freeze response is the most common — and the most damaging. It's not really about evaluation difficulty. It's about uncertainty: nobody knows what the right team looks like anymore. And in that uncertainty, the default is to do nothing. Meanwhile, an entire generation of talent — the best AI-native engineers — is being undervalued or ignored because the industry can't decide what it needs.

## Pioneer, Not Bystander

At Musinsa, we chose differently.

We decided not to sit out this chaotic era. Instead, we asked ourselves: what if we *defined* what "AI native engineer" means? What if we built a theory, a test, and an evaluation pipeline around it — not as a one-off experiment, but as a deliberate bet on the future?

This isn't recklessness. It's a calculated position: the companies that figure out AI-native hiring first will attract the best of this generation. Everyone else will be playing catch-up.

There's a line I keep coming back to: *you don't learn from the excitement behind a company's logo when you walk through the door — you learn from the people you work with day to day.* That's what we're optimizing for. Not brand prestige. Not headcount. The right people, working alongside each other, growing together — in a moment when what "the right people" means is being redefined.

We chose to find our position at the frontline and move forward as pioneers. We didn't wait for the industry to converge on a standard. We built our own.

What follows is the philosophy behind that decision — and the reasoning that shaped every aspect of our evaluation, from problem design to scoring to interviews.

## LeetCode Is Dead

Let me say it plainly: **a short algorithm problem in C or C++ is a 1-minute problem today.**

An AI agent can solve a typical LeetCode medium in seconds. It can handle most hards with a single prompt. The thing that coding tests were designed to measure — the ability to translate a problem into working code — has been commoditized overnight.

This shift is bigger than just coding tests. Andrew Ng pointed out in a Stanford CS class how dramatically the balance between building and deciding has shifted:

{{< youtube AuZoDsNmG_s >}}

The engineer-to-PM ratio used to be 7:1 or 8:1 — one person writing a spec could keep seven or eight engineers busy building it. With AI making implementation dramatically cheaper and faster, that ratio is collapsing to 2:1, even 1:1. Product management — understanding users, defining requirements, making judgment calls — hasn't sped up the same way. The bottleneck has moved. And Ng goes further: he's seeing the roles merge entirely. The fastest-moving people in Silicon Valley right now are product-minded engineers who talk to users directly, decide what to build, and direct AI to build it. One person, both roles.

If implementation is cheap, what's expensive?

**Knowing *what* to implement.**

The ability to look at an ambiguous situation, derive the actual requirements, make reasonable decisions under uncertainty, and direct AI to build the right thing — *that* is the skill that matters now. And traditional coding tests don't measure any of it.

The constrained-environment response — forcing candidates into a web sandbox with no AI tools — is the wrong direction entirely. You're not testing engineering skill. You're testing who can code like it's 2020. The skill has shifted, and the tests need to shift with it.

## The Fairness Problem

We initially considered letting candidates use their own local environments. The hypothesis was compelling: "환경 구축도 실력이다" — setting up your own development environment is itself a skill. Let people use whatever tools they're comfortable with.

But once "AI tools" became the key variable, the freedom-vs-fairness equation broke down.

In a 2-3 hour test, the difference between someone with a well-configured AI agent (Claude Pro, optimized IDE integration, custom prompts) and someone without is enormous. It's not a difference in *skill* — it's a difference in *capital*. We'd be testing who has access to better tools, not who thinks more deeply about problems.

We wanted to test problem-solving thinking, not purchasing power.

The decision: provide all candidates with an equal AI environment. Level the playing field on *tools*, then test what actually matters — the thinking.

## The Vagueness Spectrum

Here's the hardest problem in designing an AI-era coding test: **how vague should the problem be?**

This might sound like a minor design detail, but it's actually the crux of everything. Get the calibration wrong, and you're testing the wrong thing entirely.

**Full vagueness** — "Build something useful" — is still hard for AI agents because they don't know the intention. But it's also unproductive for candidates. Too open-ended, too little signal.

**Half vagueness** — "Build a REST API with these endpoints: POST /users, GET /users/{id}..." — is too easy. When you specify the API endpoints, the data model, or the UI layout, you've given away the implementation. An AI agent can fill a half-empty box with inferences in minutes.

**The sweet spot** sits between these extremes: **vague requirements with a clear outcome expectation.** The candidate must derive *what* to build. The AI can execute but can't decide what to execute.

The principle: write the problem the way a real stakeholder would — casually, with gaps, with unstated assumptions. Don't specify how to handle edge cases. Don't define NFRs. Don't list API endpoints. Just describe what the system should *do*, leave the rest to the candidate, and explicitly tell them: "anything not specified is yours to decide."

The ambiguity IS the test.

This is what separates "AI native" from "AI dependent": can you figure out *what to do* when the spec doesn't tell you?

## The Time/Scope Paradox

There's a paradox in designing tests for the AI era, and it took us a while to reason through it:

1. **Algorithm tests are dead.** A short algorithm problem is a 1-minute problem today. Not enough signal.
2. **So ask for a full service.** A real application — with a database, APIs, business logic, concurrency — is complex enough that AI can't do it on autopilot. The candidate has to *think*.
3. **But full services are hard to test.** You can't grade hundreds of submissions by hand. You need automated testing. Which means you need a way to run each submission...
4. **So ask candidates to dockerize?** But requiring Docker is a huge implementation hint. Same for specifying API endpoints, database schemas, or any test infrastructure. Every hint you give is a hint the AI can use to skip the thinking.

Every step toward testability pushes you toward specificity. And every step toward specificity makes the AI's job easier and the candidate's thinking shallower.

**The resolution came from an unexpected place: open source.**

Open source projects don't tell you how to test them by specifying exact endpoints or Docker commands in a grading rubric. They tell you by documenting themselves well. A good open source project has a README that explains how to build and run it. It has a health check. It has API documentation that another developer — or an agent — can read and use.

So instead of specifying test infrastructure, we asked candidates to document their work the way a good open source project would. *Document how to build it. Document how to run it. Document how to test it.* Then our evaluation agent — an AI itself — reads their documentation, builds their project, and tests their features.

The candidate who thinks about testability-for-others naturally writes a better README, better build scripts, better health checks, better API docs. Without being told to. Without hints.

The *way* they package their code IS part of what we evaluate.

## What We Actually Asked

With all that philosophy in place, what did the test actually look like?

We gave candidates a problem: **build a university course registration system.**

It starts with a memo from the product team:

> *"매 학기 수강신청 기간마다 서버가 다운되어 학생들의 불만이 폭주하고 있습니다. 이번에는 제대로 된 시스템을 만들어주세요."*
>
> "Every semester the server crashes during course registration and students are furious. This time, please build a proper system."

The memo lists basic features — student listing, course listing, enrollment, cancellation, timetable view. It mentions an 18-credit cap and time conflicts. And then this line:

> *"정원이 1명 남은 강좌에 100명이 동시에 신청해도, 정확히 1명만 성공해야 합니다."*
>
> "Even if 100 people simultaneously apply for a course with 1 seat remaining, exactly 1 must succeed."

That's it. No lock mechanism specified. No performance requirements. No NFRs. No authentication model. No cancellation policies. No prerequisite rules.

And the problem says explicitly: *"명시되지 않은 모든 사항은 자유롭게 결정하세요"* — "For anything not specified, decide freely."

Some might push back here: "But you *told* them what to build — a course registration system. That's not vague at all."

Is it? What *is* a course registration system? Can you fully define one from a single sentence? Think about what's hiding behind those words: concurrency under extreme contention, data integrity across multiple business rules, performance under load, failure modes, edge cases in scheduling, fairness in seat allocation. A "course registration system" is a universe of unstated complexity compressed into four words.

But here's the thing — our candidates have *lived* this problem. They've fought through broken registration systems at their own universities, cursed at ticket booking sites that crashed under load. They know, at least from the customer's perspective, what happens when these systems fail under competition. That shared experience is the seed. The problem definition starts from that sentence, but it's a starting point — not a specification.

The real question is: from that one sentence, how far can you go? How do you expand a lived frustration into a structured system design? How do you define what "the right system" even means when the people asking for it don't fully know themselves? There's a reason scientists and mathematicians start from *definitions* — because the act of defining is where the real thinking happens.

This used to be the domain of senior ICs — the ability to take an ambiguous mandate and turn it into a clear technical direction. With AI as a thinking partner, that boundary is dissolving. A junior engineer who knows how to explore a problem space with their agent — asking the right questions, stress-testing assumptions, surfacing edge cases — can now do work that used to require a team. Every engineer can operate like a senior IC. The ones who actually *do* are the ones we're looking for.

Every design principle from the previous sections converges in this problem. The vagueness is calibrated: you know *what* to build (a registration system), but you must derive *how*. The outcome expectation is clear (100 concurrent requests, 1 winner), but the mechanism is yours to choose. The problem reads like a real product team memo — because that's what you'll be reading on day one of the job.

The intentional ambiguity creates natural divergence points — places where different depths of thinking lead to meaningfully different solutions. Let's walk through a few.

## Hidden Depths

### The Data Design Trap

The problem requires generating realistic test data at startup: 10,000+ students, 500+ courses, 100+ professors — dynamically, within 1 minute.

On the surface, this seems straightforward. Generate some students, generate some courses, assign professors. Done.

But the hidden complexity is in the *relationships*. How do you assign course schedules? If you generate random time slots, you might end up with courses that all overlap — making meaningful enrollment testing impossible. How do you set capacities? Too large, and your concurrency test is trivial. Too small, and your system is unusable. How do you structure professor-course relationships? One professor per course, or realistic loads?

The candidates who thought ahead designed their data generation with the *downstream use case* in mind — creating schedules diverse enough to test time conflicts, capacities tight enough to exercise concurrency, and relationships realistic enough to surface edge cases. They treated data design as system design, not as a throwaway script.

The candidates who didn't think ahead generated flat, disconnected data — technically valid, but revealing a shallow understanding of what the system needed to *do* with that data. Their seeder worked, but it didn't create a world worth testing.

This is a design problem hiding inside a data problem. And candidates who recognized it — who documented their data generation strategy and explained *why* they made specific choices — showed exactly the kind of thinking we're looking for.

### The Concurrency Ladder

We said "handle 100 concurrent requests" but never specified *how*. No mention of pessimistic locks, optimistic locks, queues, single-machine vs. distributed. What candidates chose reveals their depth of thinking:

| Level       | Approach                                       | What it reveals                                       |
| ----------- | ---------------------------------------------- | ----------------------------------------------------- |
| Surface     | `synchronized` / in-memory queue               | Knows the word "concurrency," thinks single-JVM       |
| Competent   | DB pessimistic lock (`SELECT ... FOR UPDATE`)  | Understands DB-level serialization                    |
| Thoughtful  | Custom optimistic locking with retry           | Understands trade-offs, chose performance             |
| Deep        | Distributed lock (Redis/ZooKeeper)             | Thinks beyond single machine                          |
| Exceptional | Distributed transaction analysis               | Considers what happens when the lock *itself* fails   |

No level is "wrong." A candidate who chooses `synchronized` and clearly explains why — single-instance scope, simplicity for the stated constraints, documented trade-offs — scores better than one who throws in Redis without understanding why.

The *reasoning* matters more than the *mechanism*. That's the whole point.

Could a sharper AI or a better prompt surface these insights for the candidate? Of course. And that's fine — duck typing applies here. If it walks like deep thinking and quacks like deep thinking, it's deep thinking. The skill isn't arriving at the insight unaided. It's arriving at the insight, period — whether through your own reasoning or through a well-directed conversation with your agent.

### The Dual-Lock Insight

This is the deepest divergence point, and very few candidates discovered it on their own.

Most candidates think about concurrency purely in terms of **course-level locking** — protecting the capacity invariant. If 100 people rush for 1 seat, only 1 should succeed. Lock the course row. Done.

But that's only half the problem.

Consider what happens when the *same student* sends two concurrent registration requests — maybe they double-clicked, maybe they had two browser tabs. Two requests arrive simultaneously for different courses. Both check: "Is this student under 18 credits?" Both see 15 credits. Both pass. Both enroll. The student now has 21 credits. The credit limit is violated.

The same pattern breaks time conflict detection and duplicate enrollment checks.

**Course-level serialization** protects the capacity invariant — the shared resource across all students.

**Student-level serialization** protects per-student constraints — credit limits, time conflicts, duplicate enrollment.

You need both.

The mechanism can differ — pessimistic lock, optimistic lock with retry, application-level lock, distributed lock, serializable isolation. But the *requirement* is the same: concurrent requests from the same student must be serialized to prevent invariant violations.

The candidates who got here are the engineers we want — whether they reasoned it out themselves or through a well-directed conversation with their agent. But *arriving* is only half the story. The other half is *how* they got there, and what they understood along the way. That's what our evaluation model is designed to reveal.

## From Philosophy to Practice

All of this philosophy is meaningless without a way to measure it. We built a 3-tier evaluation model — and the structure isn't arbitrary. Each tier asks a progressively deeper question.

**Tier 1 — Make it Work.** Does the application build? Does it start? Does the health check respond? If you can't ship a running service, nothing else matters.

**Tier 2 — Basic Features.** Do the APIs work? Are business rules enforced? Does concurrency control actually hold under load? Automated test cases across multiple categories, run inside Docker containers, no human in the loop.

Tiers 1 and 2 are duck typing. Does it behave like a working system? Then it's a working system. We don't care how the candidate got there — the external behavior is the test.

**Tier 3 — Deep Thought.** This is where we go beyond duck typing. Duck typing tells you it works — it doesn't tell you whether the engineer will *keep* working at this level, or whether they'll grow. So we look inside: the candidate's prompt history, agent instructions, requirement derivation documents, data design choices, code quality, test coverage, git history, and additional implementations. An AI evaluates these dimensions — with every score backed by specific file paths and line numbers.

Tier 3 is where we distinguish *delegation* from *engagement* — and this distinction matters more than it might seem.

Anthropic's recent research on AI-assisted coding (*"How AI assistance impacts the formation of coding skills,"* Shen & Tamkin, 2026) draws a sharp line between these two modes. Engineers who delegate to AI are faster on any single task. But engineers who engage with understanding — who use AI as a collaborator to learn and verify, not just to produce — build skills that compound over time. Anthropic's own internal data shows their top performers achieve 1.2–2x throughput gains precisely because they treat AI as a thinking partner, not a replacement for thought.

That's the bet behind our scoring: **depth of thinking outweighs functional completeness.** Tiers 1 and 2 answer "can you ship?" Tier 3 answers "do you understand what you shipped — and can you grow from here?"

Two candidates can both implement dual-lock correctly. Duck typing says they're equivalent — the behavior is the same. But look beyond the interface: one explored the problem through progressively deeper prompts, documented why each serialization scope exists, and left evidence of genuine understanding in their git history. The other landed on it through a generic prompt and moved on. Same output. Very different internals.

High functional scores with shallow depth signals AI over-dependency — ships fast but can't explain why. A failed build with exceptional design thinking tells a different story — someone worth interviewing despite the technical stumble. We're not just finding who got to the right answer. We're finding who understood the question.

This is how philosophy becomes practice. In the next post, I'll walk through the actual machine we built — the automated pipeline that evaluates 360+ candidates with AI judging AI-assisted code.

---

*This is Part 1 of a 3-part series.*
*Next: **"The Machine: AI Evaluating AI-Assisted Code"** — the technical deep-dive into our automated evaluation pipeline.*
