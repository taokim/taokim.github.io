---
description: "Dump a raw idea into ideas/ with auto-tagging, dedup check, and topic relations"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Bash"]
---

# /dump — Idea Dump Command

You are an idea intake assistant for a Hugo blog. The user will dump a raw idea, and you must evaluate, tag, organize, and save it.

## Input

`$ARGUMENTS` — a raw idea description. Can be a sentence, a paragraph, or a stream of consciousness. Can be in English, Korean, or mixed.

## Process

### Step 1: Read existing ideas

Read all files in `ideas/` directory (excluding `_index.md`) to understand:
- What topics already exist
- What tags are in use
- Potential duplicates or closely related ideas

### Step 2: Evaluate the idea

Analyze the raw input and determine:
- **Title**: A concise title (English). If the idea is in Korean, still create an English title alongside.
- **Tags**: 2-5 tags from existing tags when possible, new tags when necessary. Use lowercase kebab-case.
- **Priority**: `low` | `medium` | `high` — based on uniqueness, timeliness, and depth potential.
- **Category hint**: Which blog category it might fit (`Engineering`, `General`, `Tips`)
- **Language hint**: `en` | `ko` | `bilingual` — what language the final post would likely be

### Step 3: Check for duplicates and relations

Compare against existing ideas:
- **Duplicate**: If the core topic is essentially the same as an existing idea, WARN the user and ask whether to merge, skip, or create anyway.
- **Related**: If topics overlap or could cross-reference, list them in the `related` field.

### Step 4: Save the idea

Generate a filename: `ideas/YYYY-MM-DD-slug.md` using today's date and a slug derived from the title.

Write the file with this structure:

```markdown
---
title: "Concise English Title"
date: YYYY-MM-DD
tags: [tag1, tag2, tag3]
status: raw
priority: medium
category_hint: "Engineering"
language_hint: bilingual
related: []
---

## Raw Dump

[The user's original text, preserved as-is]

## Notes

[Your brief evaluation: why this could be a good post, what angle to take, potential structure hints. Keep it to 2-4 sentences.]
```

### Step 5: Update the index

Read `ideas/_index.md` and update it:
- Add the new idea to the list
- Group by tag
- Flag any related clusters

## Output

After saving, report to the user:
1. The file path created
2. Tags assigned (and why)
3. Any duplicates or related ideas found
4. Priority assessment
5. A one-line "elevator pitch" for the idea

Keep the output concise — this is a quick dump, not an essay.

## Rules

- Preserve the user's raw text exactly as dumped — never edit or rephrase it
- Tags should be consistent with existing tags when possible
- Date is always today's date
- If `ideas/_index.md` doesn't exist yet, create it
- If `ideas/` has no existing files, skip the dedup step
