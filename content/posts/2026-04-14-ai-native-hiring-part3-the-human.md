---
title: "The Human: Judgment Beyond the Score"
date: 2026-04-14
draft: true
description: "After the machine sorted 400+ candidates, humans ran five days of interviews. What the score couldn't tell us."
tags: ["AI Native Hiring", "Interview", "Hiring", "Engineering"]
categories: ["Engineering"]
series: ["AI Native Hiring"]
ShowToc: true
TocOpen: false
---

> 🇰🇷 [한국어 버전 읽기](/posts/2026-04-14-ai-native-hiring-part3-the-human.ko/)

This is Part 3 of the AI-native hiring series. Part 1: [Philosophy, Rubric, and the 3-Tier Model](/posts/2026-02-24-ai-native-hiring-philosophy/) · Part 2: [The Machine — AI Evaluating AI-Assisted Code](/posts/2026-04-14-ai-native-hiring-part2-the-machine/)

The machine from Part 2 sorted more than 400 candidates and handed each one a personalized interview guide. Now 20+ interviewers pick up those guides and spend five days across the table from more than 100 candidates.

You can't see what a score is missing until you're across the table from someone. This post is about what humans confirmed beyond it, what we missed, and the questions we're still holding.

---

# What the Score Can't Separate

## What Results Say — and What They Don't

In Part 1 we borrowed Duck Typing: if it responds like a working system, we call it a working system. The Functional Gate scores on that principle. It doesn't ask how you got there.

That's a reasonable stance for functional verification. If the enrollment API returns the correct response, it doesn't matter whether the lock underneath is pessimistic or optimistic. Correct output is correct output.

The problem: looking at code alone, you can't tell *whose* thinking produced it.

Distributed tracing. DDD patterns. Multi-layer caching. All of that showed up in 3-hour submissions. Not from one outlier — from many. That's the ceiling. Not of the candidates — of the AI agents they were using. And that's what makes this hard: did the candidate understand what they built, or did they submit what the AI built?

This is exactly why the Quality Gate evaluates prompts and agent instructions, and demands code evidence. But even the Depth score is an *inference* — we're reading thinking-shaped traces in code and docs. We're not watching the thinking itself.

## One Shot Isn't a Signal

There's a second problem: reproducibility. A genuinely capable candidate should be able to produce consistent output with their AI agent, run after run. But we only see one submission. Does that one shot reflect real capability, or did the AI happen to cook up something good this time? Same question, different angle.

The Hustler rank from Part 2 puts this tension under a spotlight. Perfect functionality, low Depth. That might be a focused executor who thought deeply on the path to shipping. Or it might be an AI-generated solution that just happened to work.

The score alone can't tell you.

What the score couldn't confirm, the interview had to. The score worked as a filter. But to separate *human thinking* from *machine output*, we needed human eyes.

![Same result, different origin](/images/ai-native-hiring-part3-the-human/02-same-result-different-origin.png)

---

# The Script the Machine Wrote

## Questions Grounded in the Candidate's Own Code

The machine's final artifact is the interview guide. AI reads the candidate's code and writes custom questions that start *from that code*.

For example:

> **Question: Dual-Lock Strategy**
>
> *Code evidence:* `EnrollmentService.java:30-59` — two `ReentrantLock` maps serialize concurrent enrollment.
>
> ```java
> private final Map<Long, ReentrantLock> courseLocks = new ConcurrentHashMap<>();
> private final Map<Long, ReentrantLock> studentLocks = new ConcurrentHashMap<>();
> ```
>
> *"You're using two separate lock maps here. Walk me through your reasoning — why split the lock into two instead of one? What happens when two students try to enroll in each other's courses at the same time?"*

Each question comes with an expected-answer rubric. A 5 means articulating the role separation between course-level and student-level locks and naming a deadlock-avoidance strategy. A 3 means understanding the two lock scopes but missing the ordering question. A 1 means describing what the code does without being able to explain why there are two.

Follow-ups are pre-loaded too. *"If you deployed this service across multiple server instances, would this lock strategy still hold?"* The guide never points at the weakness directly — it nudges the candidate toward finding it themselves.

## The Score Profile Shapes the Interview

We don't ask every candidate the same questions. The Base (functional) × Depth (thinking) combination steers the whole conversation.

**Ace / Craftsman** (high Base + high Depth). Push the architecture to its limits.

> *"You grab an advisory lock on student ID, then a `FOR UPDATE` on the course right below it. What scenarios does each of those two locks defend against? Why can't you just use one?"*

These candidates know where their own code breaks down. *"What if you deployed this across multiple server instances?"* — pushing past the edges is what the interview is for.

**Hustler** (high Base + low Depth). Check whether the code and the docs tell the same story, and whether they can explain why they chose what they chose.

> *"You laid out six validation steps in this order for enrollment. Why is the time-conflict check last? If conflicts are more frequent than capacity rejections, wouldn't reordering be more efficient?"*

Perfect functionality but no coherent account of their own design decisions? That's an AI-overdependence signal.

**Thinker** (low Base + high Depth). Shipped a broken build but thinks sharply.

> *"Your own tests passed, but in our environment we saw capacity overrun. What could cause that difference between your test environment and ours?"*

Can they diagnose their own failure? How fast do they recover when given a hint? We're testing whether the execution gap is actually a comprehension gap in disguise.

---

# In the Room

## 100+ Candidates, 60 Minutes Each

More than 100 candidates made it to the onsite round. Over five days, 20+ interviewers ran 60-minute sessions — 30 minutes on the technical side, 30 minutes on the human side.

Every interviewer came in holding the AI-generated guide: questions pulled from the candidate's actual code, expected-answer levels, follow-ups, scoring heuristics. No need to prep generic tech questions. They could open with the candidate's own code and go from there.

## Patterns We Didn't Expect

When the results came in, the prediction was wrong in an interesting direction. Some candidates with high Functional Gate scores — service running, tests passing, concurrency holding — showed deeper understanding in the interview than their Quality Gate score suggested.

Zoom out and yes, Quality Gate scores correlated better with interview performance overall. But the *individual cases* were the interesting part. Candidates who focused on making things work seemed to have been *forced* into deeper understanding by the act of making them work. When the target is concrete — *"this API must correctly serve 100 concurrent requests"* — hitting it tends to demand real comprehension.

Quality metrics — structure, documentation, extra features — are easier to fake. A candidate who ships AI-generated output without understanding it still looks good on those dimensions.

The Hustler rank is where this pattern cracked wide open. The people from Part 2 — perfect functionality, low Depth.

One candidate scored 64 out of 120 on Depth. Thin documentation, so the AI didn't rate them highly. Then they started walking through their concurrency control in the interview and something shifted. They named the limits of a single lock on their own. When given a hint, they extended to a sharded environment. They even noted that partitioning distributes the lock but *breaks fairness* across users assigned to different buckets. The interviewer's comment: *"A level of thinking you don't always see from experienced engineers."* On pure thinking ability, they cleared the Ace bar.

Meanwhile, other Hustlers couldn't explain how their own code worked. People the machine had grouped under one label split hard in the interview room.

There were cases in the other direction too. One candidate had built strict authentication that essentially throttled concurrent access at the door. Quality Gate high, Functional Gate very low. A large gap between the two is flagged as an anomaly, and a human takes a closer look.

Reading the code carefully, the design was solid — it just didn't match the access pattern our test cases assumed. It was an edge case thrown up by 400+ different implementations. We couldn't make the tests complex enough to cover every possible path without losing signal, so we traced the code's logic manually and ran the interview anyway. The interview went extremely well.

Pure Thinkers — cases where the build flat-out failed — were actually rare. Most candidates shipped something that worked. But cases like this one, where humans caught a pattern the system hadn't predicted and rescued a strong candidate from a false negative, are exactly why you can't end hiring at the machine.

What if that reasoning had lived in a design doc instead of in the code? What if we had asked, explicitly, *"document why you chose this design and what trade-offs you considered,"* and put that document at the center of evaluation? Then the candidate knows what matters, and so does the evaluator. I'll come back to this.

That gap between score and room was where the real question lived.

---

## Same Tool, Different Conversations

Ask the interviewers whether the guide was a good tool, and most said yes. They could ask the core questions without spending hours reading the candidate's code first.

But a doubt crept in.

An interviewer reading from an AI-generated guide, scoring against a pre-baked rubric — isn't that exactly what we were suspicious of in the Hustler? We built this process to catch candidates who ship AI-generated code they don't fully understand. And here we are, evaluating them through a conversation driven by an AI-generated guide. When someone uses an AI-generated tool, where does following the tool end and judgment begin? That symmetry is uncomfortable — and it's the right kind of uncomfortable.

Of course the interviewers are experienced engineers. The guide is a starting point, not a destination. And we saw it used that way — interviewers who used the guide as a springboard, then steered the conversation off-script into terrain it didn't cover, reaching a deeper read on the candidate. Same tool, different depth of use. Whether you're the candidate or the interviewer, it comes out the same: the more powerful the tool, the more the user's judgment decides the outcome.

## Humans Need Calibration Too

In Part 2 we calibrated the machine's scoring. Humans need the same treatment.

Scorecards lived in Git. Clear rubrics went out. And still, temperature mismatches showed up — interviewers giving high scores with lukewarm comments, others giving low scores while the comments read as complimentary. Different interviewers had different internal thresholds for "good enough."

If you only give an interviewer one or two candidates, you can't see the bias. We assigned at least 4-5 per interviewer so patterns became visible. On top of that, a calibration step: compare scores against comment tone, surface per-interviewer bias, correct. (For the record: I got flagged for a leniency bias on my own calibration pass.)

## The Other 30 Minutes

The interview split into 30 minutes on the technical side and 30 on the human side. We've talked about the technical half. The human half asked different questions.

Many candidates were new grads. There wasn't enough career history to evaluate leadership against. So we focused on what matters most for a new grad: learning and adaptation. How did they work through problems they didn't know during the take-home? What did they go study *after* the test wrapped up? Not experience — the posture toward learning.

We asked about collaboration too. But new grads don't have a deep well of human collaboration stories to draw from. So AI collaboration became a surprisingly good proxy. When the AI proposed a direction, did they take it or push back? Had they ever challenged the AI's claim and — when it was wrong — changed course on their own judgment? The virtues that matter in human collaboration — critical reception, independent judgment, constructive disagreement — show up the same way with AI.

And here's something I didn't expect: the open-source principle from Part 1 came back. Candidates who wrote docs with the next reader in mind, candidates who structured their code for testability — those same candidates scored high in the human-side 30 minutes. The habit of considering another person's perspective shows up in code *and* in conversation.

---

# We Evaluated the Conversation and Missed the Design

Looking back, there's an irony in how this went.

Building the pipeline, we didn't fix things by editing the code — we edited the *instructions*. Harness engineering, as we called it in Part 2. We were already doing it. Then we turned around to evaluate candidates and leaned on prompts instead of design.

The interview results exposed the gap. As noted earlier, functional scores were useful for screening but didn't predict much beyond that — what correlated with interview-room depth was code quality and design thinking.

Code is a shadow of intent. *Why* are you building this? *Why* did you pick this architecture? *Which* trade-offs did you weigh? That context is hard to recover from the code alone. Design docs are where it lives.

This is what people are now calling Context Engineering: the quality of the context you hand to the AI determines the quality of what comes out. Context that's explicitly written down produces more consistent results than context that evaporates inside a chat window. We learned this ourselves while building the pipeline.

We asked for design docs. We just didn't put them at the center of evaluation. The machine could read *how* the code worked, but *why* it was built that way stayed in human territory. That's where the machine's limit showed. The problem of separating human intent from machine output isn't solved — but design docs are a thread worth pulling.

What we naturally did when building the pipeline, we could have applied to the evaluation itself. Great teams have been doing this long before anyone put a name on it. We could have done better this round. We'll fold it in next time.

---

# Beyond Hiring

That's what we learned from this round. But the story doesn't end at hiring. Everyone working with AI is standing in front of the same question.

## Twenty Years

I've been driving with GPS navigation for almost twenty years.

The early years were uneasy. Is the machine sending me the right way? Am I missing a faster route? The discomfort wasn't about the directions — it was about not being the one deciding.

Twenty years in, here's what's clear: route planning wasn't a problem humans needed to focus on. It just wasn't worth the attention. Nobody today seriously argues we should go back to planning our own routes. Drivers themselves will fade too, eventually — except for the ones driving purely for fun.

But the people who used to plan the route became the people who decide *where to go*. And then the people who think about *what to do* once they arrive. Every time a role disappears, a vacancy opens, and something else moves in. (Optimistic take, I know.)

There was an interesting gap during the transition. Drivers who understood the space around them could override the GPS when it was wrong. Drivers who only followed directions froze up at unexpected intersections. But even that gap faded with time — because the GPS stopped being wrong as often.

We saw the same shape in the interview room. The difference between a candidate who can't explain AI-generated code and one who understands the structure underneath it. That difference matters now. But, like GPS navigation, how long it keeps mattering — I don't know.

## Self-Driving

Think about a modern car.

Lane-keeping assist. Automatic emergency braking. Auto-parking. Where GPS used to tell you *where* to go, sensors now handle part of *how* to get there. Automation that used to live in the planning layer has moved into the execution layer.

AI agents are walking the same path. It started as autocomplete — suggesting the next line. Now you say *"build me a course registration system"* and it produces the project structure and the tests. We're in the era where three hours can surface distributed tracing and DDD.

Driving will go the same way. Once self-driving matures enough, someone in the future will ask: why did humans ever do that dangerous thing themselves? Not because machines got better at it — because the task itself stopped being a human problem. Just like navigation.

What we saw this round was already pointing that way. AI wrote the interview guide. AI did the scoring. The interviewer focused on what only a human could judge. The hours AI took off our plate went straight into conversation with the candidate. The role didn't disappear — it moved somewhere more important.

![Self-driving](/images/ai-native-hiring-part3-the-human/01-navigation.png)

---

# Looking Back

The question the series started with: *how do you evaluate engineers in the AI era?*

We tried to answer by building a machine. It worked as a filter. But separating human thinking from machine output was, in the end, not something the machine could do on its own. We also learned that we had been grading the conversation while overlooking the design. Code is a shadow of intent — and we were scoring the shadow.

Same tools, same time budget, same problem. And the spectrum of results was wide. Candidates the machine had grouped under one label split hard in the interview room. Interviewers holding the same guide ran wildly different depths of conversation. Where humans stepped in, things the machine hadn't seen came into view.

Right now, those differences matter. But, like navigation, how long they keep mattering — I don't know.

The question itself is shifting. From *"can the machine do this?"* to *"is this still a problem humans should be focused on?"*

In Part 1 I wrote that we were looking for people who understood the *question*, not people who reached the *answer*. After this hiring round, I'd add one line: the stronger the tool, the more the user's judgment decides the outcome. For now, at least.

A vacancy is opening again. We don't yet know what'll move into it. But this hiring round let us trace the edges of that vacancy. The next chapter starts there.

![The edge of the vacancy](/images/ai-native-hiring-part3-the-human/03-puzzle.png)

This has been a long series. Thank you for reading to the end. I hope it's been useful — not just for hiring, but for recognizing there's a vacancy there at all, and for making its outline a little sharper.

---

*AI-native hiring series, Part 3 of 3. Part 1: "The Philosophy" | Part 2: "The Machine" | Part 3: "The Human".*
