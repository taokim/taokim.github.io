---
description: "Import a blog post from external sources (drafts, other repos) into content/posts/ with Hugo front matter, bilingual adaptation, parallel EN/KO review, and image handling."
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Bash", "Agent"]
---

# /import-post — External Post Import

Import a blog post (or series part) from an external source into this repo as a Hugo draft. Handles language adaptation, image migration, bilingual parallel review, and tombstoning of superseded posts.

## Input

`$ARGUMENTS`:
- **Paths to source markdown**: one or more, absolute or relative. Korean-only, English-only, or both.
- **`--images <dir>`**: optional directory of images referenced by the source. Paths in the markdown are rewritten to the destination layout.
- **`--slug <slug>`**: optional override. Default: derived from title.
- **`--date <YYYY-MM-DD>`**: optional. Default: today's date.
- **`--series <name>`**: optional. Sets `series:` front matter.
- **`--supersedes <slug>[,<slug>...]`**: existing posts this import replaces. They get tombstoned.
- **`--notes <path>`**: optional companion notes/idea file to attach as `content/posts/<slug>.notes.md` (private — add to .gitignore or prefix with `_` per repo convention). Used for traceability back to the source KB.

Examples:
- `/import-post ~/ws/tk-vault/drafts/2026-04-14-foo.ko.md --images ~/ws/tk-vault/drafts/images/foo`
- `/import-post ./incoming/part2.ko.md ./incoming/part2.md --slug ai-native-hiring-part2 --supersedes 2026-02-24-ai-native-hiring-machine,2026-02-24-ai-native-hiring-under-the-hood`

## Process

### Step 1: Inspect source and plan

- Read all source files. Detect languages present (KO, EN, both).
- Read this repo's CLAUDE.md for current bilingual/tone rules and the Hugo layout.
- Determine target filenames: `content/posts/YYYY-MM-DD-<slug>.md` (EN) and/or `.ko.md` (KO).
- Resolve image destination: `static/images/<slug>/` (served at `/images/<slug>/`). Confirm by sampling an existing post's image references if uncertain.
- If `--supersedes` is set, load each superseded post and plan tombstone edits.
- Show plan to user. Confirm before writing.

### Step 2: Import source files

- Copy source markdown into the target paths.
- Replace inline H1 (`# Title`) with Hugo front matter:
  ```yaml
  ---
  title: "..."
  date: YYYY-MM-DD
  draft: true
  description: "..."
  tags: [...]
  categories: ["Engineering"]            # or other
  series: ["..."]                        # if --series
  ShowToc: true
  TocOpen: false
  ---
  ```
- Copy images to `static/images/<slug>/`.
- Rewrite image references in markdown to `/images/<slug>/<filename>`.
- Add bilingual cross-link header (per CLAUDE.md convention):
  - EN: `> 🇰🇷 [한국어 버전 읽기](/posts/<slug>.ko/)`
  - KO: `> 🇺🇸 [Read in English](/posts/<slug>/)`
- If only one language was provided: leave a placeholder file for the other language with just front matter + a `TODO: adaptation pending` marker.

### Step 3: Language adaptation (if only one language provided)

- Do NOT machine-translate. Adapt naturally per CLAUDE.md register rules.
- Read 2–3 published posts in the target language to calibrate voice before writing.
- Write full adaptation into the placeholder.
- Code blocks are shared verbatim; technical terms may stay in English in the KO version.

### Step 4: Parallel bilingual review

Launch two subagents **in parallel** (one Agent tool block with two calls):

- `blog-reviewer-en` — reviews the `.md` file against `.ko.md` + CLAUDE.md EN rules + 2–3 published EN posts.
- `blog-reviewer-ko` — reviews the `.ko.md` file against `.md` + CLAUDE.md KO rules + 2–3 published KO posts.

Each returns:
1. A list of concrete edit suggestions (line ranges + rewrite).
2. Flagged concerns (voice mismatch, 번역투, adaptation drift, CJK bold rendering issue, missing nuance).
3. A single sentence on overall readiness.

### Step 5: Apply and surface

- Apply non-conflicting edits directly.
- Surface conflicts (reviewers disagreeing on cross-language fidelity) to the user with both positions.
- Leave `draft: true`. Do not publish.

### Step 6: Tombstone superseded posts (if `--supersedes`)

For each superseded post:
- Set `draft: true` in front matter. (Or `unlisted: true` if theme supports it.)
- Prepend a banner:
  ```markdown
  > **⚠️ This post has been superseded.** A revised and consolidated version is available: [<new title>](/posts/<new-slug>/). This version is kept for reference.
  ```
- Do NOT delete — preserve for permalink continuity and reference.
- Apply to both `.md` and `.ko.md`.

### Step 7: Attach notes (if `--notes`)

- Copy `--notes` file to `content/posts/<slug>.notes.md` (or wherever private per-post notes live).
- Add source provenance at the top: `> Imported from: <original path>` and `> Related idea: <kb path if provided>`.

### Step 8: Report

- Files created / modified.
- Reviewer summary (EN + KO readiness).
- Conflicts needing user attention.
- Suggested next step (manual review → Hugo preview → publish).

## Rules

- Never machine-translate.
- `draft: true` is mandatory on import. Flipping to `draft: false` is a separate manual step.
- Tombstoned posts keep their permalinks — never rename or delete.
- If CLAUDE.md conflicts with a rule here, CLAUDE.md wins (it's the repo's authoritative voice doc).
