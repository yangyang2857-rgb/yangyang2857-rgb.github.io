# Wang Sunyang · Personal Website

**English** · [简体中文](README.zh-CN.md)

A personal site built by hand in plain HTML / CSS / JavaScript — no framework, no build
step, no third-party dependencies. Edit a file, `git push`, GitHub Pages publishes it.

**Live: <https://yangyang2857-rgb.github.io/>**

![GitHub Pages](https://img.shields.io/badge/hosting-GitHub%20Pages-2ea44f)
![No build](https://img.shields.io/badge/build-none-blue)
![No deps](https://img.shields.io/badge/dependencies-0-lightgrey)
![Theme](https://img.shields.io/badge/theme-dark%20%2F%20light-5eead4)

## Contents

- [What is in the repository](#what-is-in-the-repository)
- [Pages](#pages)
- [How it is built](#how-it-is-built)
- [Local preview](#local-preview)
- [Writing a new note](#writing-a-new-note)
- [Deployment](#deployment)
- [Naming and path conventions](#naming-and-path-conventions)
- [Browser support and fallbacks](#browser-support-and-fallbacks)
- [Deliberate trade-offs](#deliberate-trade-offs)
- [Contact](#contact)
- [Copyright](#copyright)

## What is in the repository

| Path | Purpose | Notes |
| --- | --- | --- |
| `index.html` | Home page | Single long-scroll page, eight sections: hero, about, now, skills, work, lab, timeline, contact |
| `blog.html` | Notes list | Card list plus category filter (all / frontend / productivity), with an empty-state hint |
| `404.html` | 404 page | GitHub Pages serves this automatically for unmatched paths |
| `posts/handwritten-html.html` | Note 1 | "Why I build my personal site in hand-written HTML", 2026-11-08 |
| `posts/three-habits.html` | Note 2 | "Hand the repetitive work to tools: three small habits", 2026-10-21 |
| `笔记更新指南.md` | Maintenance doc | Full steps for adding a post, changing categories, wiring prev/next |

Seven files in total, about 140 KB. No `package.json`, no `node_modules`, no build output —
clone it and the repository *is* the published site.

## Pages

**Home `index.html`**

- Hero: name, positioning (student · frontend / design / AI tooling), two entry buttons (work / notes)
- About: a short self-introduction and two figures (personal projects, daily tools)
- Now: learning / building / reading in three columns — more current than a résumé
- Skills: web development, design and typography, content, AI tooling — described as things I can
  actually do, no scores
- Work: three cards — personal site, study notes, small productivity experiments
- Lab: batch rename and archiving, turning scattered material into an index, structure before
  writing — three small workflows that run daily
- Timeline: from "how does a website even work" to "exploring and accumulating"
- Contact: email, WeChat, GitHub

**Notes `posts/*.html`**

Each post is a standalone HTML file with the same skeleton: `<title>` + `meta description` +
`article:published_time`, `.art-meta` (category · date · reading time), `<h1>`, `.toc` (collapses
into a drawer on narrow screens), `.prose` body, and `.post-nav` for previous / next.

## How it is built

### No build step

I chose hand-written HTML over Jekyll / Hexo / Vite for a simple reason: the site is a handful of
pages, and a build chain means installing an environment, running a command and waiting for output
every time a single word changes. The current model is "the file is the site": save, push, live.
What anyone sees in view-source is exactly what is running.

The cost is that styles are duplicated inline across four pages (home, list, 404, article
template), so a colour change happens in four places. That repetition is deliberate — it buys zero
dependencies and zero waiting.

### Dual theme

- Theme is driven by `<html data-theme="dark|light">` with two sets of CSS variables: dark
  background `#060a14`, light background `#f6f7fb`, accents `#5eead4` and `#0e9887`
- The button in the top-right corner writes the choice to `localStorage`; until the visitor picks
  one, it follows the system `prefers-color-scheme` and listens for changes
- The important detail: a small synchronous script in `<head>` sets `data-theme` **before the first
  paint**, so a dark-mode visitor never sees a white flash
- Switching also updates `meta[name="theme-color"]`, so the mobile browser address bar follows

### Typography and responsive layout

- System font stack: `-apple-system` / `Segoe UI` / `PingFang SC` / `HarmonyOS Sans SC` /
  `Hiragino Sans GB` / `Microsoft YaHei`. No web fonts are loaded, so there is no FOUT
- Headings and spacing scale fluidly with `clamp()` (e.g. `clamp(56px, 9vw, 82px)`) instead of a
  pile of breakpoints
- The grid narrows at three breakpoints (920 / 820 / 620px): three columns → two → one; the
  two-column hero stacks vertically on small screens

### Interaction

- A reading-progress bar at the top, throttled with `requestAnimationFrame` to at most one
  calculation per frame
- Sections fade and rise into view via `IntersectionObserver` rather than a `scroll` listener
- Category filtering on the notes list is pure frontend: match on `data-cat`, update
  `aria-pressed` on toggle, show or hide the empty state

### Accessibility

- The first focusable element on every page is a "skip to main content" link pointing at `#main`
- Navigation, article TOC and prev/next links all carry `aria-label`; decorative arrows and
  separators are marked `aria-hidden="true"`
- The theme button expresses state with `aria-pressed` and rewrites its `aria-label` to describe
  the target state ("switch to light mode" / "switch to dark mode")
- Under `prefers-reduced-motion: reduce`, fades and smooth scrolling are turned off
- Semantic structure: one `<h1>`, sections as `<section>` + `<h2>`, body copy as `<p>` / `<ul>` /
  `<pre><code>`

### Sharing and indexing

- Every page has `canonical`, `description`, `og:*` and `twitter:card`
- The favicon is an inline `data:` SVG (dark rounded square with the character 王) — no extra request
- `meta name="color-scheme"` declares light and dark support so form controls and scrollbars follow
  the theme

## Local preview

Pages use relative paths, so double-clicking a file mostly works — but a local server is better,
because `file://` changes path resolution and `localStorage` behaviour:

```bash
git clone https://github.com/yangyang2857-rgb/yangyang2857-rgb.github.io.git
cd yangyang2857-rgb.github.io
python3 -m http.server 8000
# open http://localhost:8000/
```

## Writing a new note

The full checklist lives in [`笔记更新指南.md`](笔记更新指南.md). The essentials:

1. Copy `posts/three-habits.html` as the template and rename it to lowercase English with hyphens
   (e.g. `css-layout-notes.html`)
2. Change six places: `<title>`, `meta description`, `article:published_time`, `.art-meta`, `<h1>`,
   and the `.prose` body
3. Update `.post-nav` at the bottom: left points to the previous post, right to the next; leave
   `<div class="empty-slot">` where there is none
4. Insert the new card at the **top** of `<ul class="post-list">` in `blog.html` (newest first) and
   update the `count` in the filter buttons; add a filter button only when opening a new category
5. Push and wait for the Pages build

## Deployment

- Hosting: GitHub Pages, source = `main` branch, repository root
- Domain: `yangyang2857-rgb.github.io` — no `CNAME` file, no custom domain
- No GitHub Actions workflow: nothing needs building, Pages just serves the files as they are
- Publish delay: usually a few tens of seconds after pushing; force-reload (Ctrl/Cmd + Shift + R) if
  a cache is still warm

## Naming and path conventions

| Convention | Notes |
| --- | --- |
| Post filename | Lowercase English with hyphens. Chinese names work, but the URL becomes a long `%E4%B8%AD%E6%96%87` string that is awkward to share |
| Relative paths | Links between files inside `posts/` **omit** `../`; links back to the homepage or list use `../index.html`, `../blog.html` |
| Category consistency | A card's `data-cat` and a filter button's `data-filter` must match exactly, spaces included — one character off and the filter shows nothing |
| Dates | Both the post's `article:published_time` and the list's `<time datetime>` must be updated |
| Escaping in code blocks | `<` and `>` in body text must be written `&lt;` and `&gt;`, or the browser eats them as tags |

## Browser support and fallbacks

Targets the major browsers of the last two years (Chrome / Edge / Safari / Firefox, mobile
included). Everything used is a stable feature: CSS variables, Grid, `clamp()`,
`IntersectionObserver`.

Fallbacks in place:

- All `localStorage` reads and writes are wrapped in `try/catch` — private mode throws, and the
  theme degrades to following the system without breaking the page
- Where `matchMedia(...).addEventListener` is unavailable, it falls back to `addListener`
- With `prefers-reduced-motion` active, all motion is disabled and only static styling remains

## Deliberate trade-offs

- **Styles duplicated four times**: home, list, 404 and article each carry their own inline CSS.
  A colour or spacing change touches four files — the payoff is zero build and zero requests
- **Posts are maintained by hand**: adding one means copying the template, editing content and
  adding a card. A local generator becomes worth writing past roughly twenty posts
- **No sitemap / RSS**: indexing relies on search engines crawling naturally, nothing is submitted
- **No dark-mode image handling**: the site uses no photos today. If that changes, each image needs
  two variants or a filter

## Contact

| Channel | Address |
| --- | --- |
| Email | [3383927177@qq.com](mailto:3383927177@qq.com) |
| WeChat | `ILOVEHUTAOMUCH` |
| GitHub | [@yangyang2857-rgb](https://github.com/yangyang2857-rgb) |

## Copyright

No `LICENSE` file is attached; copyright in the code and the text belongs to the author. To
republish an article or reuse the page design, get in touch by email or WeChat first.
