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

## JS behaviors (bottom `<script>`)

Four small vanilla-JS features, no framework:

1. Hamburger toggles `.open` on `#mobile-menu`; `.mobile-link` clicks close it.
2. Typewriter loop over the `roles` array into `#typewriter` — edit that array to change the rotating hero titles.
3. `IntersectionObserver` adds `.visible` to any element with class `reveal` (one-shot; it unobserves). New animated content must carry `class="reveal"` or it will stay invisible.
4. Scroll listener highlights the active `.nav-links a` by matching `section[id]` offsets, using inline `style.color`.

## Content conventions

Résumé content (experience entries, skills tags, project cards, contact links) is hardcoded HTML. Adding an item means duplicating an existing sibling card/tag and editing its text — keep the existing class names so the section's grid and reveal animation continue to apply.