# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Running the project

```bash
npm run dev   # starts browser-sync, auto-reloads on content.md / style.css / index.html changes
```

Opens at `http://localhost:3000`. The browser reloads automatically whenever `content.md` is saved — no manual refresh needed.

> The article must be served over HTTP. Opening `index.html` via `file://` fails because the script fetches `content.md` and browsers block that cross-origin.

There is no build step and no test suite.

## Architecture

This is a single-page Ukrainian-language editorial article for the **vector.** media brand. It consists of three files:

- **`content.md`** — the only file authors edit. It is the article source of truth.
- **`index.html`** — the full page shell plus an inline `<script>` that fetches and transforms `content.md` at runtime.
- **`style.css`** — all visual styles, including responsive breakpoints.

### Content pipeline (runtime, no build)

`index.html` fetches `content.md` at page load, then the inline script transforms raw Markdown into styled HTML components using a sequence of DOM passes. The transformation rules are:

| Markdown syntax | Rendered component |
|---|---|
| `# Title` / `**Дек:** …` before `---` | Article headline and deck (overrides HTML defaults) |
| First paragraph after `---` | `.lead` (serif large intro paragraph) |
| `## NN — Кікер. Title` | Section `<h2>` with orange kicker label and accent rule |
| `> **Головне зі статті** + list` | `.takeaways` summary box |
| `> quote … > — attribution` | `.pq` pull quote |
| `> **Читайте також:** text →` | `.ralso` read-also interrupt |
| `**NN → Title.** text` (consecutive paragraphs) | `.process` numbered steps block |
| `До:` / `Після:` + lists | `.compare` before/after two-column grid |
| `Ключові цифри:` + list | `.stats` metric tile grid |
| `` `[__]` `` | Yellow editorial placeholder pill |
| `**Теги:** #one #two` | `.tags` chip row |

The script also auto-generates the right-rail table of contents from `<h2>` elements, drives the reading-progress bar, and implements TOC scroll-spy.

### CSS design tokens

All colours, max-widths, and the reading column width are CSS custom properties on `:root` in `style.css`. The primary accent is `--accent: #ff3b1f`. The article body max-width is `--read-w: 720px` inside a three-column grid (`rail-l | body | rail-r`); both rails collapse below 1080 px.

### Fonts

`Manrope` (UI/headings) and `Merriweather` (serif body/pull-quotes) are loaded from Google Fonts via `<link>` in the `<head>`.

## Editing content

Edit only `content.md`. The comment block at the top of that file documents every supported Markdown convention. The `---` line separates the frontmatter (title + deck) from the article body. Changes appear immediately on refresh (the script adds a cache-busting query string).
