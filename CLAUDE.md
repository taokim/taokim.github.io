# Blog Project Guidelines

## Project Info
- **Repo**: https://github.com/taokim/taokim.github.io
- **Framework**: Hugo + PaperMod theme
- **Hosting**: GitHub Pages (GitHub Actions deploy)
- **Author**: Tao Kim (tao.sbkim@gmail.com)
- **Blog URL**: https://taokim.github.io

## Blog Tone
- Balanced: technical articles + casual tips/guides
- Personal and approachable, not overly formal or grandiose
- Two purposes: (1) engineering decisions & strategic thinking, (2) practical everyday knowledge

## Bilingual Posting Convention

### Default
Every post is published in both English and Korean, unless:
- Content is clearly Korean-specific (e.g., Mac 한영 전환 guide) → Korean only
- Content is clearly English-specific → English only
- User explicitly requests a single language

### English First
Always write the English version first, then Korean.

### Short vs Long Posts
- **Short posts**: Single file — English first, `---` separator, then Korean
  - File: `content/posts/YYYY-MM-DD-slug.md`
  - Title: `"English Title / 한국어 제목"`
- **Long posts**: Separate files with mandatory cross-links
  - English: `content/posts/YYYY-MM-DD-slug.md`
  - Korean: `content/posts/YYYY-MM-DD-slug.ko.md`
  - English file: `> 🇰🇷 [한국어 버전 읽기](/posts/YYYY-MM-DD-slug.ko/)`
  - Korean file: `> 🇺🇸 [Read in English](/posts/YYYY-MM-DD-slug/)`

### Title & Label Convention
When bilingual text must appear together (site title, post title, etc.): English first, then Korean.

### Content Quality
- Do NOT machine-translate — adapt content naturally for each language
- Technical terms can stay in English in the Korean version
- Code blocks are shared (no translation needed)

### Writing Register: EN vs KO

**Tone & Metaphor**
- EN can be playful and colloquial with tech metaphors (punchy one-liners, extended analogies)
- KO uses the same concepts but in a dignified, natural register — avoid comic/cute phrasing that may feel dismissive in Korean context
- When a metaphor lands differently across languages, adapt freely. The concept matters, not the exact words.

**번역투 (Translated-sounding Korean) — Avoid**
- Inverted structures: "X의 선택, Y가 아닌" → rewrite in natural Korean word order
- "솔직하게 말하겠다:" style openers → drop or start directly with the statement
- "English Title: 한국어 부제" bilingual headers → just use Korean, or just English if the term is well-known
- Stacked 의 particles ("이 시리즈의 모든 것의 출발점") → restructure
- Direct calques: "방정식이 깨졌다" → "균형이 무너졌다"
- Programmatic verbs for people: "최적화하는" → "신경 쓰는"
- Translated compound nouns: "하류 유스케이스" → "이 데이터로 실제 뭘 할 건지"

**Section Titles**
- Adapt freely between languages — don't force literal parallel structure
- EN titles can be clever/catchy; KO titles should be clear and natural
- e.g., "The Fear and the Freeze" → "얼어붙은 업계", "Pioneer, Not Bystander" → "먼저 움직이기로 했다"

**Technical Metaphors**
- When a programming concept (e.g., duck typing) is used as a metaphor: EN can extend playfully; KO references the concept name but describes the idea in natural Korean
- When the metaphor has conceptual limits, acknowledge the boundary rather than stretching it

## Commit Convention
- Commit whenever a logical group of changes is finalized
- Use descriptive commit messages (not "fix" or "update")
