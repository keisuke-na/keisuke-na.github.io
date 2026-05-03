# Project: keisuke-na's notes (Quartz blog)

Personal tech blog. Published in English at https://keisuke-na.github.io. Built with Quartz 4 + pnpm, deployed via GitHub Actions on push to `main`.

## Article style

- **All posts are written in English.**
- Optimize for a **~1-minute skim**. Cut prose aggressively.
- **Each section: max 3 lines of prose, target 2.** Tables and bullet lists do not count toward the line limit; prefer them when content is dense.
- Tone: terse, factual, no hype. Numbers over adjectives.
- Use Obsidian-flavored markdown (Quartz supports it): `[[wikilinks]]`, `> [!note]` callouts, `$math$`, fenced code with language hints.

## Article structure (default)

```
## TL;DR        — 1–2 sentences with the punchline (numbers if applicable)
## Background   — why this matters, in 1–2 sentences
## Approach     — what was done, params, setup
## Results      — table preferred for numbers
## Gotchas      — table or bullets for issues + fixes
## References   — links only
```

Skip sections that don't apply. Reorder if it reads better. Don't pad.

## Frontmatter

```yaml
---
title: <Sentence case, no trailing punctuation>
description: <one line, used for OG/meta>
tags: [<lowercase, kebab-case>]
draft: false
date: YYYY-MM-DD
---
```

- New articles default to `draft: true` in the template; flip to `false` when ready to publish.
- Date is the publish date. Today's date is available in the user's global CLAUDE.md.

## File conventions

- Posts live under `content/posts/`.
- Slug: `lowercase-hyphenated.md` (e.g. `stabilizing-gemini-tts-with-rvc.md`). The slug becomes the URL.
- Images: place next to the post or under `content/assets/`, embed with `![[image.png]]`.

## Authoring workflow

- Local preview: `pnpm run serve` → http://localhost:8080 (hot reload).
- Build check: `pnpm run build`.
- Push to `main` deploys via `.github/workflows/deploy.yml`.
- Templates: `content/templates/article.md` (inserted via Obsidian's core Templates plugin).

## What not to do

- Do not write multi-paragraph "intro" or "conclusion" sections.
- Do not add filler like "In this post, we will...". State the result first.
- Do not invent numbers or quotes. If the source material lacks data, say so or omit.
- Do not commit anything under `private/`, `.obsidian/`, or `node_modules/` (already gitignored).