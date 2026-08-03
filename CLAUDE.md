# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A personal portfolio site for Vanditkumar Patel (Senior Android Developer), served by GitHub Pages at https://vandit067.github.io from the `main` branch.

There is no build system, no package manager, no dependencies, and no tests. The entire site is a **single self-contained file, `index.html`** — markup, all CSS in one `<style>` block, and all JS in one `<script>` block at the bottom. The only external requests are Google Fonts (Inter, Space Grotesk) and the GitHub-hosted avatar image.

## Workflow

- Preview: open `index.html` in a browser directly (`open index.html`), or `python3 -m http.server` in the repo root.
- Deploy: pushing to `main` publishes. There is no CI step, so verify visually in a browser before pushing — a broken `index.html` is a broken production site.
- `.idea/` is JetBrains IDE state and is untracked; leave it alone.

## Structure of index.html

Everything is delimited by banner comments (`/* ═══ SECTION NAME ═══ */`) rather than files. Match that convention when adding code.

- `:root` custom properties at the top of the `<style>` block define the whole design system (`--bg`, `--surface`, `--card`, `--border`, `--blue`, `--purple`, `--cyan`, `--green`, `--text`, `--muted`, `--radius`, `--nav-h`). Use these variables instead of literal colors; the dark theme is the only theme.
- Page sections in document order, each an `<section id="...">`: `hero`, `about`, `skills`, `experience`, `projects`, `contact`. CSS for each lives in the matching banner block.
- Responsive rules are **not** colocated with their components — they are collected in four breakpoint blocks at the end of the `<style>` block (≤900px tablet, ≤768px mobile, ≤480px small, ≤360px very small), plus a few inline `@media` overrides. When changing layout, check whether a later breakpoint block overrides it.
- A `prefers-reduced-motion` block sits last in the `<style>` block and blanket-disables animations/transitions. New motion generally needs no special handling, but anything that reveals content via animation does — make sure its resting state is visible there.
- `.sr-only` (in the reset block) is the screen-reader-only utility.

## JS behaviors (bottom `<script>`)

Four small vanilla-JS features, no framework:

1. `setMenu(open)` toggles `.open` on `#mobile-menu` and keeps the `#hamburger` button's `aria-expanded`/`aria-label` in sync — always go through it rather than touching the class directly. Escape closes and restores focus.
2. Typewriter loop over the `roles` array into `#typewriter` — edit that array to change the rotating hero titles. Skipped under reduced motion, which renders `roles[0]` statically. The animated span is `aria-hidden`; the `.sr-only` sibling in `.hero-role` is what screen readers get, so keep the two in rough sync.
3. `IntersectionObserver` adds `.visible` to any element with class `reveal` (one-shot; it unobserves). New animated content must carry `class="reveal"` or it will stay invisible.
4. A second `IntersectionObserver` highlights the active `.nav-links a` via inline `style.color`. Deliberately not a scroll handler — reading `offsetTop` per scroll event forced synchronous layout and caused mobile jank. Don't reintroduce that pattern.

## Generated images

Two PNGs at the repo root are **generated, not hand-drawn** — each has an HTML source in `_sources/` that is rendered headless and screenshotted. Edit the `.html`, then re-render; never touch the `.png` directly.

| Source | Output | Size | Used by |
|---|---|---|---|
| `_sources/og-image.html` | `og-image.png` | 1200×630 | `og:image` / `twitter:image` in `index.html` |
| `_sources/linkedin-banner.html` | `linkedin-banner.png` | 1584×396 | LinkedIn profile cover — **not referenced by the site** |

```
python3 -m http.server 8765          # from the repo root
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new --disable-gpu --hide-scrollbars --force-device-scale-factor=1 \
  --virtual-time-budget=5000 --window-size=1200,630 \
  --screenshot=og-image.png "http://localhost:8765/_sources/og-image.html"
```

The sources live in `_sources/` because GitHub Pages runs Jekyll, which skips any file or directory beginning with `_` — so they stay versioned without being served. They previously sat at the repo root, where `/og-image.html` resolved to a contentless one-screen graphic carrying the site's name; both also carry `robots: noindex, nofollow` as a fallback. Keep new render sources in `_sources/`, and do **not** add a `.nojekyll` file without moving them somewhere else first.

Both sources duplicate the site's `:root` colors and font links rather than importing them — keep them in sync with `index.html` by hand when the palette changes.

**Headless viewport gotcha:** `--window-size` alone does **not** set the layout viewport for a top-level page — it crops instead. Screenshotting `index.html` at a specific width (e.g. to check a breakpoint) requires loading it inside a fixed-width `<iframe>` and screenshotting that wrapper. Both image sources above hardcode their own `body` dimensions, so they render directly.

**LinkedIn banner safe area:** LinkedIn overlays the profile photo on the lower-left of the cover and crops roughly the outer 20% off each side on mobile. Keep content within about x300–1250; the layout is centered at 52% for this reason.

## Content conventions

Résumé content (experience entries, skills tags, project cards, contact links) is hardcoded HTML. Adding an item means duplicating an existing sibling card/tag and editing its text — keep the existing class names so the section's grid and reveal animation continue to apply.