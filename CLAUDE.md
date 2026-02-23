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

## Commit Convention
- Commit whenever a logical group of changes is finalized
- Use descriptive commit messages (not "fix" or "update")
