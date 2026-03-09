---
description: "Review a polished draft with multi-persona critiques — accept/reject each, loop until done"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Bash", "Agent", "AskUserQuestion"]
---

# /review — Post-Polish Document Review

Review a polished blog draft through multiple reviewer lenses. Each critique is individually evaluated, accepted or rejected with reasoning and alternatives. Works on EN and KO simultaneously. Loops until the user says **done**.

## Scope — Content Only

This skill reviews and edits **content text only**. It does NOT:
- Read, modify, or reference idea files in `ideas/`
- Change front matter (title, date, draft, tags, status, etc.)
- Publish or unpublish anything
- Change any metadata or status fields

The only files it touches are `.md` / `.ko.md` post files (or `internal/` docs), and only their **body content** — everything below the closing `---` of the front matter.

## Input

`$ARGUMENTS` — one of:
- A filename or slug from `content/posts/` or `internal/` (e.g., `ai-native-hiring` or `2026-02-24-ai-native-hiring-philosophy`)
- The word `list` — show all draft posts (`draft: true`) and internal docs
- No argument — show available posts and ask which to review

## Reviewer Personas

Every review round activates **all five** personas. Each persona reads both the EN and KO versions (if both exist) and produces critiques relevant to their domain.

### 1. Structure & Flow
- Section balance (too heavy? too thin?)
- Logical progression — does each section earn the next?
- Opening hook effectiveness
- Conclusion strength — does it land?
- Cross-reference consistency between EN and KO versions (parallel structure where appropriate, but NOT forced mirroring)

### 2. Tone & Voice
- Blog voice consistency (personal, approachable, not grandiose)
- Audience fit — is it speaking to the right reader?
- EN: punchy, playful where appropriate, technically grounded
- KO: dignified, natural register — no comic/cute phrasing that feels dismissive
- Confidence calibration — overclaiming vs. hedging too much

### 3. Language Quality (CRITICAL)
This reviewer exists specifically to catch unnatural language in both versions.

**KO checks — 번역투 / 어색한 표현:**
- Inverted structures: "X의 선택, Y가 아닌" → rewrite in natural Korean word order
- "솔직하게 말하겠다:" style openers → drop or start directly
- Stacked 의 particles ("이 시리즈의 모든 것의 출발점") → restructure
- Direct calques from English: "방정식이 깨졌다" → "균형이 무너졌다"
- Programmatic verbs for people: "최적화하는" → "신경 쓰는"
- Translated compound nouns: "하류 유스케이스" → "이 데이터로 실제 뭘 할 건지"
- "English Title: 한국어 부제" bilingual headers → pick one language
- Unnatural connectors: "그러므로", "따라서", "그럼에도 불구하고" used excessively
- Overly formal endings mixed with casual tone (consistency)

**EN checks — Awkward / Stilted phrasing:**
- Passive voice overuse ("it was decided" → "we decided")
- Nominalization bloat ("the implementation of" → "implementing")
- Weasel words ("somewhat", "arguably", "it could be said")
- Filler transitions ("In this section, we will discuss...")
- Inconsistent register (mixing academic prose with casual tone)
- Run-on sentences or overly nested clauses

### 4. Technical Accuracy
- Are claims backed or at least reasonable?
- Code examples: correct syntax, realistic usage
- Terminology consistency within and across EN/KO
- Technical terms in KO — are they left in English where natural, or awkwardly translated?

### 5. Reader Empathy
- "Would I keep reading after paragraph 2?" test
- Information density — too much jargon? too sparse?
- Value delivery — does each section give the reader something?
- TL;DR test — can you summarize what the reader gains?
- Length appropriateness — is it earning its word count?

## Process

### Step 1: Load the draft

1. Find the target post file(s) in `content/posts/` or `internal/`
2. Read both EN (`.md`) and KO (`.ko.md`) versions if they exist
3. If only one language exists, review that single file
4. **Do NOT read idea files** — review is based solely on the post content as written

### Step 2: Run all reviewers

For each reviewer persona, produce critiques. Each critique is a discrete, actionable item.

**Critique format:**

```
### [Persona Name] — [EN|KO|Both]

#### Critique #N: [Short title]
- **Where**: [Section/paragraph/line reference]
- **Issue**: [What's wrong and why it matters]
- **Current**: "[Quoted text from the draft]"
- **Verdict**: ✅ ACCEPT (apply fix) | ❌ REJECT (keep as-is)
- **Reasoning**: [Why this verdict — consider the specific context, the overall document flow, and whether the "fix" would actually improve things]
- **If accepted — Alternative A**: "[Suggested replacement]"
- **If accepted — Alternative B**: "[Different approach]" (optional, when multiple valid fixes exist)
```

### Step 3: Evaluate each critique honestly

This is the core of the skill. For each critique:

**To ACCEPT (recommend the fix):**
- The issue genuinely hurts readability, accuracy, or tone
- The fix is clearly better than the original
- The issue is not a matter of pure style preference

**To REJECT (keep the original):**
- The "issue" is actually fine in context — the reviewer persona is being overly strict
- The suggested fix would break flow, tone, or rhythm elsewhere
- The original phrasing serves a deliberate purpose (emphasis, callback, stylistic choice)
- The critique is technically correct but the fix is worse than the problem

Do NOT default to accepting everything. A good review has a healthy mix of accepts and rejects. Over-accepting produces bland, over-edited text. The goal is to improve the draft, not sanitize it.

**Cross-language coherence**: When a critique affects one language, check whether the same issue exists in the other. If a KO fix changes meaning, the EN version may need a corresponding adjustment (and vice versa). Flag these as linked critiques.

### Step 4: Present the review

Present all critiques grouped by persona. After the full review, provide a summary:

```
## Review Summary

- **Total critiques**: N
- **Accepted (recommended fixes)**: X
- **Rejected (keep as-is)**: Y
- **EN-specific**: A | **KO-specific**: B | **Both**: C

### Recommended actions (accepted critiques):
1. [Brief description] — [EN|KO|Both]
2. ...

### Kept as-is (rejected critiques):
1. [Brief description] — [Reason for keeping]
2. ...
```

### Step 5: Wait for user response

The user can respond with:

- **"apply all"** or **"accept all"** — Apply all accepted fixes. Do NOT apply rejected ones unless user overrides.
- **"apply N"** or **"accept N"** — Apply specific critique number(s). e.g., "apply 1, 3, 5"
- **"override N"** — Override a rejection — apply the fix even though it was rejected. Ask user to pick Alternative A or B.
- **"reject N"** — Override an acceptance — keep the original for that critique.
- **"feedback on N: [text]"** — User provides additional context on a critique. Re-evaluate that specific critique with the new information and present updated verdict.
- **"re-review"** or **"next round"** — After applying changes, run another full review round on the updated text.
- **"done"** — Exit the review loop. Report final state.

### Step 6: Apply changes

When applying fixes:
1. Edit **only body content** in the appropriate file(s) — EN, KO, or both
2. **NEVER edit front matter** (anything between the opening and closing `---`)
3. For linked cross-language critiques, apply both sides
4. After applying, briefly report what changed

### Step 7: Loop

Return to **Step 5** and wait for the next user command. Continue until the user says **done**.

When the user says **done**:
- Report total changes made across all rounds
- List files modified
- No status updates, no idea file changes, no front matter modifications

## Rules

### Boundary — Content Only (NON-NEGOTIABLE)
- **NEVER read or modify idea files** in `ideas/`
- **NEVER modify front matter** — no title, date, draft, tags, status, or any YAML field changes
- **NEVER publish or unpublish** — do not flip `draft: true/false`
- **NEVER update status fields** anywhere (ideas, posts, internal)
- **Only edit body content** — the text below the front matter closing `---`
- If a front matter issue is spotted (e.g., wrong tag), mention it to the user but do NOT fix it

### Review Integrity
- **Honest evaluation**: Do NOT accept every critique. Reject when the original is fine.
- **Both languages simultaneously**: Never review EN alone when KO exists (and vice versa).
- **번역투 is a priority**: The Language Quality reviewer should be especially rigorous on KO naturalness. This is the #1 reason this skill exists.
- **No machine-translation artifacts**: If the KO version reads like translated English, flag it aggressively.
- **Preserve author voice**: Fixes should sound like the author, not like a committee.
- **Context over rules**: A "rule violation" that serves the piece well should be rejected, not fixed.
- **Alternatives are required**: Every accepted critique must offer at least one concrete alternative. Never say "this should be better" without showing how.
- **Respect deliberate choices**: If something looks intentional (repeated pattern, callback, stylistic device), note it in reasoning before accepting or rejecting.
- **No scope creep**: Review the text as written. Don't suggest adding new sections, topics, or restructuring the entire piece (unless it's truly broken).
- **Cross-language awareness**: A fix in one language may require a corresponding fix in the other. Always check.
