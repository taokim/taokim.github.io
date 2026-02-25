---
description: "Publish a draft blog post — flip draft:true to draft:false and update idea status"
allowed-tools: ["Read", "Write", "Edit", "Glob", "Grep", "Bash"]
---

# /publish — Publish a Draft Post

Flip a draft blog post from `draft: true` to `draft: false`, making it visible on the live site after the next deploy.

## Input

`$ARGUMENTS` — one of:
- A post slug or filename from `content/posts/` (e.g., `ai-native-hiring-philosophy`)
- The word `list` — show all posts with `draft: true`
- No argument — list all drafts and ask which to publish

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

## Rules

- **ALWAYS confirm** before flipping the draft flag — this is a publish action
- If both EN and KO versions exist, publish both together (unless user specifies otherwise)
- Never change post content — only the `draft` field (and optionally `date`)
- If the post is already `draft: false`, inform the user and do nothing
