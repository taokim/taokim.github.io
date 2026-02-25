---
description: "Polish a raw idea from ideas/ into a blog post draft"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Bash"]
---

# /polish — Idea to Blog Post

Convert a raw idea from `ideas/` into a blog post draft in `content/posts/`.

## Input

`$ARGUMENTS` — one of:
- A filename or slug from `ideas/` (e.g., `some-idea` or `2026-02-24-some-idea`)
- The word `list` — to show all ideas sorted by priority, with status
- The word `ready` — to show only ideas with status `ready`

## Process for polishing

### Step 1: Load the idea

Read the specified idea file from `ideas/`. If ambiguous, list matches and ask.

### Step 2: Discuss the approach

Before writing, briefly present to the user:
- The raw dump content
- Suggested post structure (headings, sections)
- Suggested tone and length
- Whether it should be bilingual (check `language_hint`)
- Whether it should be short-form (single file) or long-form (separate files)

Wait for user confirmation or adjustments before proceeding.

### Step 3: Write the post

Follow the blog's bilingual posting convention from CLAUDE.md:
- **English first**, then Korean
- **Short posts**: Single file with `---` separator
- **Long posts**: Separate `.md` and `.ko.md` files with cross-links
- Use the existing front matter patterns (tags, categories, ShowToc, etc.)
- Maintain the blog's personal, approachable tone
- **ALWAYS set `draft: true`** in front matter. Polish creates drafts only — publishing is a separate step via `/publish`.

### Step 4: Update the idea status

After the post draft is created:
- Update the idea file's `status` to `polished`
- Add a `polished_to` field with the post path
- Do NOT set `draft: false` — that is `/publish`'s job

### Step 5: Report

Tell the user:
- What files were created (as `draft: true`)
- Suggested next steps: review the draft, then `/publish` when ready

## Rules

- Always discuss structure with the user before writing the full post
- Respect the blog's tone: personal, approachable, not grandiose
- Do NOT machine-translate — adapt content naturally for each language
- Technical terms can stay in English in the Korean version
- Code blocks are shared across languages
- **NEVER publish** — always `draft: true`. Use `/publish` to go live.
