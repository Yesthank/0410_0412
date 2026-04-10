# CLAUDE.md

Guidance for AI assistants (Claude Code, etc.) working on this repository.

## Project overview

This repository is a **single-file, static HTML travel itinerary** for a 2박 3일
(2-night, 3-day) trip to **Fukuoka, Japan** on **2026.04.10 → 2026.04.12**.
The page is written in **Korean** (`<html lang="ko">`) and is designed
**mobile-first** as a personal travel guide (flights, lodging, per-day
activities, restaurant candidates, an Arsenal match block, a checklist, etc.).

There is no application, no backend, no framework, and no build step — the
entire project is one file.

## Repository layout

```
/
├── index.html   # The entire site (HTML + embedded CSS + small vanilla JS)
└── CLAUDE.md    # This file
```

- `index.html` is ~1020 lines. Structure inside the file:
  - Lines ~1–11: `<head>`, meta tags, Google Fonts + Pretendard preconnect/links.
  - Lines ~12–721: `<style>` — all CSS lives inline here.
  - Lines ~723–1005: `<body>` — semantic sections of the itinerary.
  - Lines ~1007–1018: tiny `<script>` with an `IntersectionObserver` for
    `.reveal` scroll animations.

## How to run / preview

No build system. Open `index.html` directly in a browser, or serve the folder
statically, e.g.:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000/
```

There is **no** `package.json`, lockfile, bundler, linter, formatter, or test
suite. Do not introduce one unless the user explicitly asks.

## Content structure (body)

The body is organized top-to-bottom as a scrollable itinerary:

1. **`section.hero`** (`index.html:726`) — full-screen dark hero with
   Japanese/Korean title, date chips, and a decorative 桜 character.
2. **`div.tickets.wrap`** (`index.html:743`) — two `.ticket` cards:
   `.ticket.dep` (departure TAE→FUK) and `.ticket.ret` (return FUK→PUS),
   styled like boarding passes.
3. **`div.wrap`** (`index.html:806`) — the main content column containing:
   - **Day 1** (`index.html:807`) — `.day` header + `.card` blocks for
     lodging/transit, dinner candidates, and a live-jazz bar.
   - **Day 2** (`index.html:866`) — morning walk, lunch candidates, LP-shop
     grid, an Arsenal vs. Bournemouth `.match` block, and 포장마차.
   - **Day 3** (`index.html:931`) — checkout timeline, airport dinner
     candidates.
   - **Checklist** (`index.html:972`) — interactive `.check-item`s that toggle
     a `.done` class on click.
   - Donki Hote note (`index.html:995`).
4. **`div.footer`** (`index.html:1002`).

## Component / CSS conventions

All CSS uses **plain class selectors** (no BEM, no utility framework, no
nesting). Components and their modifiers live in the `<style>` block and
follow a consistent vocabulary:

- **Design tokens** live in `:root` at `index.html:15` — always prefer these
  over hard-coded values:
  - Colors: `--bg`, `--bg-card`, `--bg-deep`, `--bg-warm`, `--ink`,
    `--ink-light`, `--ink-faint`, `--red` / `--red-soft` / `--red-deep`,
    `--blue` / `--blue-soft`, `--gold` / `--gold-soft`,
    `--sakura` / `--sakura-deep`, `--green` / `--green-soft`.
  - Fonts: `--font-title` (Noto Serif KR), `--font-body` (Pretendard),
    `--font-accent` (Gowun Batang), `--font-hand` (Gaegu),
    `--font-mono` (DM Mono).
  - Shape: `--radius`, `--radius-sm`, `--shadow-sm/md/lg`.
- **Layout wrapper**: `.wrap` (`index.html:294`) — centers content and caps
  width at `768px` via a media query at `index.html:300`. The page is
  mobile-first; this is the only breakpoint.
- **Reusable blocks** (each has a matching CSS section in the same order):
  - `.hero*` — hero section bits.
  - `.ticket`, `.ticket.dep` / `.ticket.ret`, `.ticket-top/-bottom/-route/
    -label/-code/-city/-mid/-dur/-field` — boarding-pass card.
  - `.day`, `.day-tag`, `.day-num`, `.day-info`, `.day-flow` — day header.
  - `.card`, `.card-accent` — generic content card. Accent color is set via a
    modifier class on `.card-accent`: `.red`, `.blue`, `.gold`, `.green`,
    `.sakura` (`index.html:377`).
  - `.tl`, `.tl-item`, `.tl-time`, `.tl-text`, `.tl-note` — vertical timeline
    inside a card.
  - `.resto-list`, `.resto`, `.resto-n`, `.resto-body`, `.resto-name`,
    `.resto-desc`, `.resto-addr` — restaurant candidate list.
  - `.badge`, plus color modifiers `.red`, `.gold`, `.green`, `.blue`
    (`index.html:522`) — small tag chip.
  - `.lp-grid`, `.lp-item`, `.lp-icon`, `.lp-name`, `.lp-note` — LP-shop grid.
  - `.match`, `.match-label`, `.match-vs`, `.match-team`, `.match-badge`,
    `.match-tname`, `.match-tinfo`, `.match-x`, `.match-meta`, `.match-coyg`
    — Arsenal match block.
  - `.check-list`, `.check-item`, `.check-box`, `.check-label`, plus the
    `.done` state modifier.
  - `.footer`, `.footer-sakura`.
  - `.reveal` / `.reveal.show` — the animation hook (see below).

## Interaction / JavaScript

There are only two pieces of behavior on the page:

1. **Scroll reveal** — `index.html:1007`: an `IntersectionObserver` adds
   `.show` to any element with class `.reveal` as it enters the viewport, then
   unobserves it. To animate a new block on scroll, add `class="reveal"` to
   it; no JS changes needed.
2. **Checklist toggle** — inline `onclick="this.classList.toggle('done')"` on
   each `.check-item` (`index.html:985`). When adding new checklist rows,
   keep the same inline handler so no script wiring is needed.

Keep JS **vanilla and tiny**. Do not introduce frameworks, build tools, or
external JS dependencies.

## Conventions & gotchas

- **Language**: all user-facing strings are Korean, with occasional Japanese
  (e.g., 福岡, 桜, 富一) and English labels (e.g., `DEPARTURE · 출발`,
  `PREMIER LEAGUE · MD 32`). Preserve this bilingual style when editing and
  do not translate existing copy unless asked.
- **Emoji** are used heavily as inline iconography (✈️ 🍴 🎷 🌿 💿 ⚽ 🏮 🛍️
  🎹 🏨 ✅ 🛒 🍢 🥞 🗼 🎸 🎶 🎵 🔴 🍒 🍺). This is intentional stylistic
  choice — keep using emoji the same way rather than swapping in SVG icons.
- **Mobile-first**: the single breakpoint is `@media (min-width: 768px)`
  (`index.html:300`). Design new elements for narrow viewports first.
- **Self-contained file**: keep HTML, CSS, and JS in `index.html`. Do not
  extract to separate files or introduce a bundler unless explicitly asked.
- **External resources**: the only network deps are Google Fonts and the
  Pretendard CDN (`cdn.jsdelivr.net`). Don't add more.
- **No accessibility audit has been done**; when adding interactive elements
  (like the click-to-toggle checklist), consider `role`/`aria-*` if the user
  asks for a11y improvements, but do not refactor existing markup
  unprompted.
- **Dates are fixed** (2026.04.10–04.12). Flight numbers (TW215 / TW236),
  booking codes, seat numbers, and the Arsenal fixture are concrete personal
  data — do not invent or "correct" them.

## Editing guidance for AI assistants

- Prefer small, surgical edits with the `Edit` tool; read the file first.
- When adding a new card, copy the nearest existing `.card` block as a
  template, pick a `.card-accent` color that fits the surrounding palette,
  and include `reveal` in the class list so it animates on scroll.
- When adding a new restaurant row, match the existing `.resto` markup
  shape exactly (`.resto-n` number, `.resto-body` with `.resto-name` +
  `.resto-desc` + `.resto-addr`, and an optional trailing `.badge`).
- Keep the DOM order aligned with the trip chronology (Day 1 → Day 2 →
  Day 3 → Checklist → Footer).
- Do **not** add documentation files, READMEs, or tooling config unless the
  user asks.

## Git / branch workflow

- Default branch: `main`.
- Feature/documentation work for Claude Code sessions happens on branches
  such as `claude/add-claude-documentation-*`. Commit with clear messages
  and push with `git push -u origin <branch>`. Do **not** open a pull
  request unless the user explicitly requests one.
