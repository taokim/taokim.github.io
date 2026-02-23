---
title: "Starting This Blog / 블로그를 시작하며"
date: 2026-02-10
draft: false
description: "Why I started this blog — engineering decisions, practical tips, and everything in between"
tags: ["meta"]
categories: ["General"]
ShowToc: false
---

I've been wanting to write things down for a while.

Over the years, I've accumulated thoughts and experiences — things I figured out through work, side projects, and everyday problem-solving. Useful stuff, but easy to forget if I don't write it down somewhere proper.

So I made this blog. It serves two purposes:

**Sharing engineering decisions and strategic thinking.** As an engineer working at scale, I regularly face decisions that affect systems, teams, and business outcomes — technology choices, architectural trade-offs, migration strategies, organizational patterns. Writing about them helps me think more clearly, and I hope it helps others navigating similar problems.

**Sharing practical, everyday knowledge.** Not everything has to be a deep architectural essay. Sometimes the most useful thing is a 5-minute guide that saves someone an hour of frustration.

**Writing with AI — doable, but not "just ask it to write."** I use [Claude Code](https://docs.anthropic.com/en/docs/claude-code) to help with this blog, but it's not as simple as prompting an LLM and hitting publish. Getting decent output requires a real pipeline. Mine looks roughly like this:

1. **Dump** — throw a raw idea into a private `ideas/` folder, auto-tagged and dedup-checked
2. **Refine** — develop the angles, connect it to existing ideas, decide if it's worth writing
3. **Polish** — turn it into a bilingual draft (`draft: true`), with a style guide (`CLAUDE.md`) enforcing tone, structure, and natural Korean (not translation-sounding)
4. **Publish** — final review, flip the flag, push

Each step is a custom slash command backed by the repo's own conventions. The AI follows the rules because they're codified in the project — not because I ask nicely each time. The whole thing lives in [this repo](https://github.com/taokim/taokim.github.io) if you're curious.

I'll write in both English and Korean. Let's see where this goes.

---

예전부터 뭔가 글로 정리해두고 싶었다.

몇 년간 일하고, 사이드 프로젝트 하고, 이것저것 삽질하면서 쌓인 경험이 있는데, 제대로 적어두지 않으면 결국 잊어버리게 된다.

그래서 블로그를 만들었다. 목적은 두 가지다.

**엔지니어링 의사결정과 전략적 사고를 공유하는 것.** 규모 있는 시스템을 다루다 보면 기술 선택, 아키텍처 트레이드오프, 마이그레이션 전략 같은 결정을 자주 하게 된다. 글로 정리하면 생각이 명확해지고, 비슷한 고민을 하는 사람들에게도 도움이 되지 않을까 한다.

**실용적이고 일상적인 지식을 공유하는 것.** 모든 글이 깊은 아키텍처 에세이일 필요는 없다. 때로는 5분짜리 가이드가 누군가의 1시간 삽질을 줄여주기도 한다.

**AI로 글쓰기 — 가능하지만, "그냥 써달라고 하면 되는" 건 아니다.** 이 블로그는 [Claude Code](https://docs.anthropic.com/en/docs/claude-code)를 활용해서 쓰고 있다. 다만 LLM에 프롬프트 한 번 던져서 바로 발행하는 방식은 아니다. 쓸 만한 글이 나오려면 나름의 파이프라인이 필요하다.

1. **Dump** — 떠오른 아이디어를 `ideas/` 폴더에 자동 태깅과 중복 체크를 거쳐 저장
2. **Refine** — 어떤 각도로 쓸지 다듬고, 기존 아이디어와 연결하고, 쓸 가치가 있는지 판단
3. **Polish** — 영/한 초안으로 작성 (`draft: true`). 스타일 가이드(`CLAUDE.md`)가 톤, 구조, 번역투 방지까지 관리
4. **Publish** — 최종 검토 후 발행

각 단계는 이 레포의 컨벤션에 맞춰 만든 커스텀 슬래시 커맨드로 동작한다. AI가 규칙을 따르는 건 매번 부탁해서가 아니라, 프로젝트에 규칙이 코드화되어 있기 때문이다. 궁금하면 [레포](https://github.com/taokim/taokim.github.io)를 직접 보면 된다.

영어와 한국어 섞어서 쓸 예정이다. 어디까지 갈지 한번 보자.
