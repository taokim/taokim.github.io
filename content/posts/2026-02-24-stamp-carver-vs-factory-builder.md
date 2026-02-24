---
title: "The Stamp Carver vs. The Factory Builder"
date: 2026-02-24
draft: false
description: "Breaking through the senior engineer growth wall — from 'Micro Coding' to 'Macro Coding'"
tags: ["career", "engineering", "leadership", "architecture"]
categories: ["Engineering"]
ShowToc: true
TocOpen: false
---

> 🇰🇷 [한국어 버전 읽기](/posts/2026-02-24-stamp-carver-vs-factory-builder-ko/)
>
> This post was originally published on the [MUSINSA Tech Blog](https://techblog.musinsa.com/%EB%8F%84%EC%9E%A5-%ED%8C%8C%EB%8A%94-%EA%B0%9C%EB%B0%9C%EC%9E%90-vs-%EA%B3%B5%EC%9E%A5-%EC%A7%93%EB%8A%94-%EA%B0%9C%EB%B0%9C%EC%9E%90-b33dddf5daef) in Korean. This is the English adaptation.

**A note from 2026:** I originally wrote this before the current wave of AI swept through our industry. Since then, the landscape has shifted even further than I described. The progression from 'How' to 'Why'? AI has pushed it one step beyond — to **'What.'** As implementation costs plummet with AI-assisted coding, the 'Micro' (How) is being commoditized faster than anyone expected. The 'Macro' perspective — asking 'Why' and especially 'What should we build?' — has become not just valuable but essential. Looking back, this framing feels even more relevant now, perhaps even understated at the time. That's why I'm resharing it here — as a lens for reading what's happening around us.

---

**Subtitle: A Senior IC's story of breaking through the 'wall' with 'Macro Coding'**

Hi, I'm Tao Kim. I work at MUSINSA as a Distinguished Engineer and company-wide tech lead.

This is my first post on the MUSINSA tech blog. I plan to share various topics and thoughts going forward. For the first topic, I'd like to talk about growth in my role as a Senior IC (Individual Contributor).

As a Senior IC, I participate in company-wide key decisions and work as an engineer. Having been in this role for a while, I often get questions about career and growth. With MUSINSA recently formalizing the IC Career Path alongside the manager track, I've been regularly introducing this "new growth ladder" to colleagues.

The stories I've shared in tech talks, 1:1 meetings, and various internal channels — I'm publishing them here hoping they'll help not just MUSINSA colleagues but engineers more broadly.

## 1. To You Who Feel the 'Wall' of Growth

This post is for engineers who are shedding the junior title to move into senior roles, or those who already have the senior title but feel stuck about what comes next.

Have you ever had thoughts like these?

- "I'm doing my best with clean code, refactoring, TDD... why does my growth feel stagnant?"
- "My code is getting better, but why isn't my impact at the company growing proportionally?"
- "I'm supposed to be a senior developer, but what I do doesn't feel very different from when I was a junior."
- "Why does my salary feel like it's hit a ceiling?"

If any of these resonate, you probably need an **expansion of perspective**. I believe the key to breaking through this growth 'wall' lies in shifting from **'Micro Coding'** to **'Macro Coding'**.

## 2. Is Your Coding 'Micro' or 'Macro'?

To discuss growth, let me define two perspectives that I think help engineers understand.

### 'Micro Coding'

- The perspective of a **'Point'**.
- Focuses on **the quality of the code itself** and **implementing the current feature** — variable names, function logic, class design, test coverage, code conventions.
- This is a fundamental and powerful virtue of a great developer. Clean code, TDD, refactoring all belong here.

### 'Macro Coding'

- The perspective of a **'Flow'**.
- Focuses on **how this code affects the entire system and business**.
- It's about how 'points' come together to form 'lines' and 'surfaces' — seeing the bigger picture.

Many engineers aim to become masters of 'Micro Coding,' but at some point, they hit a 'wall' of problems (or salary negotiations) that can't be solved from that perspective alone.

![Engineer's Job Title and Scope](/images/stamp-carver-vs-factory-builder/engineer-scope.png)
*Engineer's Job Title and Scope (with Gemini)*

### [Self-Assessment] Do I Have a 'Macro' Perspective?

Here are some questions to check how much 'Macro' perspective you've developed:

- In the past month's code reviews, beyond pointing out variable names, have you pointed out 'failure flow of this logic' or 'business impact'?
- Can you explain within 30 seconds which business KPI (e.g., purchase conversion rate) the feature you're currently building contributes to?
- When solving problems, have you ever asked 'why should we do this?' and thought about a better 'flow' before jumping to 'how to implement it'?
- Are you confident that if traffic grows 10x in a year, the current architecture can handle it?
- When an API you built fails, can you picture what cascading failures might occur across the calling service (Caller) and the services you call (Callee)?

If answering 'Yes' to these questions feels difficult, you're likely still in the 'Micro' perspective.

## 3. What Makes 'Macro Coding' Different?

So what specifically differs in the 'Macro Coding' perspective?

### Perspective 1: See the 'Flow,' Not the 'Point'

While 'Micro' focuses on "how can I make this Spark function more efficient?", 'Macro' thinks "if this Spark Job fails, the entire data pipeline halts — **how do we design the recovery flow on failure?**"

If code is a 'point,' data and requests are a 'flow.' The 'Macro' perspective designs 'drains and detours' for when this flow gets blocked or bursts. (e.g., Retry, Circuit Breaker, Dead Letter Queue)

### Perspective 2: Ask 'Why' Before 'How'

The 'Micro' perspective focuses on 'how' to implement the given ticket well. The 'Macro' perspective first asks **'why'** this ticket was given to us.

- **Micro:** "When you press this button, make user info pop up in a modal."
- **Macro:** "Is showing user info in a 'modal' really the best approach? **'Why'** do they need to see user info? Wouldn't it serve the business goal better to show just the key info via a Nudge instead of a modal?"

Asking 'why' isn't encroaching on the PM or planner's territory — it's **the core competency of a senior engineer who solves business problems better with technology**.

### Perspective 3: Look Beyond 'Code' to Beyond 'API'

A real example I experienced: An engineer reported "I keep getting HTTP timeouts calling Service A's API. My logic has no issues — seems like Service A's problem."

- The **'Micro' perspective** stops at 'my code's' boundary. "My code is fine. Service A isn't responding. My responsibility ends here."
- The **'Macro' perspective** looks beyond 'the API.' "**Why** is Service A timing out? Should I look at Service A's logs too? Maybe Service A depends on Service B, and B is dying? Oh, Service A is making too many synchronous calls. I should propose switching this to an async queue architecture."

The 'Macro' perspective aims for **the success of the 'entire flow,' not just 'my code.'** Systems are organically connected, and the thread to solving problems often lies beyond 'my code's boundary.'

## 4. Design 'Factories,' Not 'Stamps'

To draw an analogy between 'Micro' and 'Macro' — it's the difference between a 'stamp' and a 'factory.'

- The **'Micro' perspective** is close to a **'craftsman'** who carves a stamp with exquisite precision. The focus is on how beautiful and error-free the stamp is.
- The **'Macro' perspective** is close to an **'architect'** who designs a **'factory'** that stamps out 10,000 of those stamps per second. The quality of the stamp itself is baseline; they design 'what flow' the stamp goes through, 'how to mass-produce' it, and 'how to recover when the factory stops.'
- This is, in addition to beautiful craftsmanship, the reality of 'industrialization' that cannot be refused — a matter of scale and impact.

A few years ago in Japan, an automated robot for stamping 'Hanko' (personal seals) became a hot topic. How should we view this phenomenon?

![Automated stamp robot factory line](/images/stamp-carver-vs-factory-builder/stamp-robot-factory.png)
*Automated stamp robot factory line (with Gemini)*

This symbolically shows what can happen when you're **too immersed** in the 'Micro' perspective alone. No matter how exquisitely you hone the 'stamp-carving skill' (Micro), if 'machines' that automate the process appear, an individual's craftsmanship can become meaningless.

**But we need to go one step further.**

Is the true 'Macro' perspective about efficiently making 'stamp-carving robots'? Or is it, like the 'factory' analogy suggests, asking "why are we stamping at all? Can we replace this flow with e-Signatures?"

The 'stamp robot' may be an extreme automation of 'How,' but it's missing the fundamental question of 'Why' — it might be **a sophisticated example of automating the wrong flow.** The 'Macro' perspective means not staying at 'craftsmanship' or 'machines (automation),' but constantly questioning **whether 'the current flow itself' is right** and leading the larger 'industrialization' change.

![The Stamp Carver vs The Factory Builder](/images/stamp-carver-vs-factory-builder/stamp-carver-vs-factory-builder.png)
*The Stamp Carver vs The Factory Builder (with Gemini)*

## 5. In Closing: Where Should Your Growth (and Salary) Be Bet?

'Micro Coding' is the solid foundation of an engineer. Without this foundation, 'Macro' is nothing but a castle in the sky.

But most organizations and education focus on 'Micro.' Everyone preaches 'clean code,' but nobody teaches how to design 'business flows.'

From the enterprise perspective, the leaders they need are engineers who can evolve from 'Micro' to 'Macro.' By the law of supply and demand, the scarcity of engineers with a 'Macro' perspective inevitably leads to higher valuation.

If your growth feels stagnant, or your salary feels like it's hit a 'wall,' it's time to reflect on whether your efforts are only focused on carving 'stamps,' and whether you should be betting on building the bigger picture — 'factories.'

Lastly, I'd like to close by thanking my Team MUSINSA Senior IC colleagues who helped greatly in writing this post.

---

### Postscript 1: 'Micro' and 'Macro' Are Simply Different Levels, Not a Matter of Superiority

Please don't misread this as "so Micro coding isn't important?" Just as both macro and micro economics are important, both perspectives matter. Imagining only 'Macro' while neglecting 'Micro' is building a castle on sand. The 'Macro' perspective becomes truly powerful when it's built on the solid foundation of 'Micro.' Don't let your focus on Micro let you lose sight of Macro — and vice versa.

This post also isn't about arguing who is the better engineer. It's simply that most engineering organizations and education are concentrated on 'Micro,' so the importance of 'Macro' tends to be overlooked. From the enterprise perspective, the leaders they need are engineers who can build 'Macro' on top of 'Micro.' Since the supply of engineers with a 'Macro' perspective is scarce relative to demand, I wanted to share the practical reality that they can be valued higher (in salary negotiations).

### Postscript 2: This Post Is Not for Every Senior Engineer

The role of a senior engineer varies by organization and individual inclination. Some create irreplaceable value by diving to the extremes of 'Micro' to become a 'Specialist' in a specific domain. (e.g., kernel tuning, compilers, 3D rendering engines)

This post is not about domain specialists — it's focused on growth for 'Product Engineers' like myself, who **solve business problems with technology**.

### Postscript 3: On 'Engineering Surface'

In the main text, I used the metaphor of a stamp's 'surface.' This aligns with the concept commonly called **'Engineering Surface'** or **'Surface Area'** in engineering.

'Engineering Surface' refers to all the 'contact points' where a system communicates with the outside world.

- **API specs:** Endpoint and JSON structure like POST /users.
- **Function signatures:** Input and output of calculatePrice(user, item).
- **Class interfaces:** Externally callable Public Methods.

The 'Micro' craftsman focuses on how elegantly, cleanly, and precisely to polish this 'Surface.'

But the 'Macro' perspective sees this 'Surface' differently. In a Microservice Architecture, if one service's 'Surface' becomes too large and complex (e.g., over 100 APIs and unmanageable), that's a signal that the 'Surface' is too bloated — grounds for **service separation**. In other words, the 'Macro' perspective designs not just the beauty of the 'Surface' itself, but the appropriate **'size' and 'boundaries'** of that 'Surface.'
