# TLDR

A multi-article static site of magazine-style recaps of long-form videos —
read in 10–15 minutes instead of watching. Hosted on GitHub Pages at
https://bogdandraghici.github.io/TLDR/.

## Layout

```
/
├── index.html          landing page — a bare <ol class="articles">, no
│                       header/title/footer (intentionally stripped)
├── <slug>/index.html   one folder per article; slug == URL path
└── .gitignore          excludes transcript.json, .DS_Store, .claude/
```

URLs are clean folder paths (`/anthropic-product-team-cat-wu/`), never `.html`.
GitHub Pages is enabled on `main` at root; rebuilds take ~30–60s after push.

## Adding a new article

Use the `video-to-webpage` skill (lives at
`~/.claude/skills/video-to-webpage/`). It generates the recap directly into
`<slug>/index.html`, prepends a `<li>` to the landing page, commits, and
pushes. Don't hand-write articles — the skill enforces layout invariants
(timestamp links, verbatim snippets, sidebar segments, etc.).

To draft without publishing, tell the skill "draft" / "don't publish" /
"local only". It writes a `<slug>-recap.html` in CWD and skips the push.

## Conventions

Every article file is **single self-contained HTML** — no external CSS, JS,
fonts, or CDN assets. The whole file must work offline and via email.

Design tokens live in `:root` at the top of each file (oklch colors, system
sans/mono, no serif). The template is the source of truth; don't drift.

Key components and their invariants:

- **Embedded player** — `.player-wrap` is an in-flow 16:9 placeholder; the
  visible chrome is `.player-frame` and moves to `position: fixed` when
  scrolling past the hero. Don't move `.player-wrap` itself — that causes a
  scroll-jump.
- **Takeaways** — compact one-line items: counter + bold headline + faint
  inline description.
- **Speakers** — collapsed by default behind `<details class="speakers-disclosure">`.
  Visually merged with the takeaways above (no section gap).
- **Detailed breakdown** — every idea has timestamp pill + 1–2 sentence
  paraphrase + verbatim transcript snippet (hidden, kept for verification).
- **Transcript sidebar** — right rail on wide screens; one `<li data-s="N">`
  per segment; hovering a breakdown idea highlights the matching segment
  window.
- **Landing page** — just the article list. No header, no section label,
  no colophon. New entries go at the top (reverse chronological).

## Commits

- Commit message format for new articles: `Add recap: <title>` (one line, no body).
- Other commits use a short subject line + optional body explaining the *why*.
- Don't bundle unrelated changes into a recap commit.

## Don't

- Don't commit `transcript.json` or intermediate working files.
- Don't load fonts/icons/CSS from CDNs.
- Don't rename a slug after publishing — the URL is the article's
  permanent identity.
- Don't force-push or rewrite published history.
