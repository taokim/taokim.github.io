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

### Flags

- `--internal` — Polish into an internal document (`internal/`) instead of a blog post. See **Internal Mode** below.

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

## Internal Mode (`--internal`)

When `--internal` is passed, the output is an internal document — not a blog post.

### Differences from blog mode

| Aspect | Blog (default) | Internal (`--internal`) |
|--------|----------------|------------------------|
| Output path | `content/posts/` | `internal/` |
| Format | Hugo front matter, `draft: true` | Plain markdown, no front matter |
| Bilingual | Yes (per CLAUDE.md conventions) | No — write in the language that fits the audience (usually Korean for internal) |
| Tone | Personal blog voice | Professional but warm — speaking to colleagues or new hires |
| Cross-links | EN/KO cross-links | Not applicable |

### Internal process

Steps 1–2 are identical (load idea, discuss approach). Then:

**Step 3 (Internal):** Write the document as plain markdown in `internal/YYYY-MM-DD-slug.md`. No Hugo front matter. Structure and tone should match the audience (e.g., a welcome letter reads differently from an architecture doc).

**Step 4 (Internal):** Update the idea file's `status` to `polished` and add `polished_to` pointing to the `internal/` path.

**Auto-detection hint:** If the idea has the `internal` tag, suggest `--internal` mode to the user before proceeding.

## Rules

- Always discuss structure with the user before writing the full post
- Respect the blog's tone: personal, approachable, not grandiose (blog mode)
- Respect a professional, warm tone for internal documents (internal mode)
- Do NOT machine-translate — adapt content naturally for each language
- Technical terms can stay in English in the Korean version
- Code blocks are shared across languages
- **NEVER publish** — always `draft: true` (blog) or unfinalized (internal). Use `/publish` to ship.
