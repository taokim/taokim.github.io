---
description: "Publish a draft blog post — flip draft:true to draft:false and update idea status"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Bash"]
---

# /publish — Publish a Draft Post

Flip a draft blog post from `draft: true` to `draft: false`, making it visible on the live site after the next deploy.

## Input

`$ARGUMENTS` — one of:
- A post slug or filename from `content/posts/` (e.g., `ai-native-hiring-philosophy`)
- The word `list` — show all posts with `draft: true` (blog) or unfinalized internal docs
- No argument — list all drafts and ask which to publish

### Flags

- `--internal` — Finalize an internal document instead of a blog post. See **Internal Mode** below.

## Process

### Step 1: Find the draft

Search `content/posts/` for matching files. If the post has both `.md` and `.ko.md` versions, identify both.

### Step 2: Confirm with the user

Show the user:
- File(s) to be published
- Post title and date
- Whether it has a bilingual counterpart
- Ask for explicit confirmation before proceeding

### Step 3: Publish

For each matched file:
1. Change `draft: true` to `draft: false` in the front matter
2. Optionally update the `date` field to today if the user requests it

### Step 4: Update the idea file

Search `ideas/` for an idea with a `polished_to` field pointing to this post, or by matching slug:
1. Update `status` to `published`
2. Add `published_date: YYYY-MM-DD`

If no matching idea file is found, skip this step silently.

### Step 5: Report

Tell the user:
- Which files were published
- Remind them to commit and push to trigger GitHub Actions deploy

## Internal Mode (`--internal`)

For internal documents, "publish" means **finalize** — mark the document as done and update tracking.

### Internal process

**Step 1:** Search `internal/` for matching files (by slug or filename).

**Step 2:** Confirm with the user — show the file path, title, and ask for explicit confirmation.

**Step 3:** No draft flag to flip. The document is already in its final form. If the user wants last-minute edits, do them now.

**Step 4:** Update the idea file — set `status` to `published` and add `published_date: YYYY-MM-DD`.

**Step 5:** Report what was finalized. No deploy reminder (internal docs aren't deployed via GitHub Actions).

**Auto-detection hint:** If `--internal` is not passed but the matched idea has the `internal` tag or `polished_to` points to `internal/`, suggest internal mode.

## Rules

- **ALWAYS confirm** before flipping the draft flag or finalizing — this is a publish action
- If both EN and KO versions exist, publish both together (unless user specifies otherwise)
- Never change post content — only the `draft` field (and optionally `date`) for blog posts
- Never change document content for internal docs unless the user requests edits during finalization
- If the post is already `draft: false` (blog) or already `published` (internal), inform the user and do nothing
