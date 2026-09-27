# Personal Portfolio Website — Technical & Design Report

---

## 1. Project Overview

The page is a one-page personal portfolio ("get in touch" page) for a backend engineer. It was rebuilt from scratch against a hard coursework constraint:

| Constraint | Requirement | Delivered |
| --- | --- | --- |
| Total size | ≤ 600 lines of code (HTML + CSS combined) | **598 lines** (HTML 257 + CSS 341) |
| Formatting | Prettier must stay in use, code must stay Prettier-formatted | `prettier --check` **passes** — Prettier defaults, since this branch carries no config file |
| CSS variables | No `var()`, no custom properties | **0** occurrences |
| CSS delivery | No external stylesheet — internal CSS only | one `<style>` block; `style.css` deleted |

Measured facts about the finished artifact:

- **598 lines total**, 15,921 bytes.
- **341 CSS lines** = 60 rules inside `<style>`; **257 HTML lines**.
- **Zero external dependencies**: no external CSS, no web fonts, no JavaScript, no CDN requests. The only asset is the local hero photograph `img/PEN_4350.jpg`.
- The page renders identically in file-open mode and over HTTP (no build step, no server requirement).

---

## 2. Design Concept

### 2.1 Concept statement

> A calm, dark "engineer's terminal-meets-editorial" portfolio: a wide dark canvas, one accent colour for interactive intent, monospace accents for technical voice, and a strict card-based rhythm that lets content — not decoration — carry the page.

The design targets a recruiter or collaborator who spends 30 seconds scanning. Therefore the page front-loads identity (name, role, one-line pitch, direct links) and then walks through evidence in a fixed order: experience → skills → education → contact.

### 2.2 Colour system

The palette is **Catppuccin Mocha** (a widely used dark theme) reduced to 12 values. The pallete documented here as the single source of truth.

| Hex | Role in the page |
| --- | --- |
| `#1e1e2e` | Page background (base) |
| `#181825` | Card / panel / form surfaces |
| `#11111b` | Footer bar (crust) |
| `#313244` | Card borders, hero photo frame, table row rules |
| `#45475a` | Input borders, button borders, chip borders |
| `#cdd6f4` | Primary text |
| `#a6adc8` | Secondary text (lede, bullets, labels) |
| `#9399b2` | Muted text (`.sub` — metadata, footer note) |
| `#cba6f7` | Primary accent: role line, section eyebrows, submit button |
| `#b4befe` | Secondary accent: brand suffix, chips, bullet markers, nav CTA |
| `#74c7ec` | Tertiary accent: highlighted "email" action button |
| `#89b4fa` | Focus ring (`:focus-visible` outline) |

Contrast was tuned for AA compliance; `#9399b2` replaced an earlier, darker muted tone that measured only **4.44:1** against the card surface.

### 2.3 Typography

- **Body:** system font stack — `system-ui, -apple-system, "Segoe UI", Roboto, sans-serif`, base size 16 px, line-height 1.6.
  Using the system stack removes a network dependency and keeps the page usable on networks where external font hosts are blocked or slow.
- **Mono accent:** `ui-monospace, SFMono-Regular, Menlo, Consolas, monospace`, applied through the `.mono` class to the role line, section eyebrows, the brand, and contact links — the "technical voice" of the page, with no extra font download.
- **Type scale (single scale, no fluid clamp):** `0.78 / 0.82 / 0.83 / 0.85 / 0.9 / 0.92 / 0.93 / 0.95 / 1.1 / 1.12 / 1.15 / 1.7 / 2.6 rem`, plus `2 rem` for `h1` at mobile.
- **Display weights:** `h1` 800 at `2.6rem` with `-0.03em` tracking; `h2` 800 at `1.7rem` with `-0.02em`; `h3` 700 at `1.12rem`. Tight tracking on large headings is what gives the page its contemporary, deliberate feel.

### 2.4 Layout and spacing system

- **Container:** `main` is `max-width: 1040px; margin: 0 auto; padding: 0 20px`. The navigation bar repeats the same 1040 px/20 px geometry so the header and content align on one axis.
- **Vertical rhythm:** section blocks use `padding-top: 56px` with `gap: 24px`; the hero uses `64px 0 40px`; the footer is separated by `margin-top: 64px`. Every value comes from a small step set of **6 / 8 / 12 / 14 / 16 / 18 / 24 / 40 / 56 / 64 px**.
- **Radii:** `6px` (chips) · `10px` (buttons, inputs) · `16px` (cards, table panel, form) · `24px` (hero photo).
- **Borders:** always 1 px in `#313244` or `#45475a`; borders and surfaces, not shadows or gradients, define depth.
- **Sticky navigation:** 64 px tall, `position: sticky; top: 0`, translucent `rgba(24,24,37,0.96)` with a 1 px bottom border.

### 2.5 Component vocabulary

The 60 CSS rules resolve to a deliberately small set of reusable components:

1. **Reset (7 rules)** — `border-box` on everything, margin reset on text elements, list/tabular resets, image block display, inherited link and form-control colours, and a global `:focus-visible` ring.
2. **Navigation** — `.nav` / `.nav-in` / `.brand` / `.nav-links` / `.nav-cta`.
3. **Hero** — `.hero` (two-column grid `1.4fr 1fr`), `.hero-copy`, `h1`, `.role`, `.lede`, `.links`, `.btn`, `.btn--accent`, `.photo` (square-cropped via `aspect-ratio: 1/1` + `object-fit: cover`, framed by an 8 px `#313244` border).
4. **Section shell** — `.block` (vertical flow + gap), `.eyebrow` (uppercase mono label), `h2`.
5. **Cards** — `.card` used by experience, organisations, and education; `h3` inside for titles; `.sub` for metadata.
6. **Lists** — `.bullets` with a `▹` marker drawn in CSS (`li::before`), so no icon files or inline SVG are required.
7. **Chips** — `.chips` / `.chip` pill labels for the technology stack.
8. **Data table** — `.table-wrap` panel + `table` / `th` / `td` for the skills matrix.
9. **Contact block** — `.contact-grid` (two columns) and `.contact-list` (direct channels).
10. **Form** — `.form`, `.form-row` (two-column row), `.form label` (stacked label + control), `.input`, `button`.
11. **Footer** — `footer` / `.footer-in` with a single centred note.

### 2.6 Responsive strategy

A single breakpoint, `@media (max-width: 640px)`, is intentional: it keeps the stylesheet inside the line budget while covering the layout that actually breaks (phones).

| At ≤ 640 px | Change |
| --- | --- |
| `.hero`, `.contact-grid`, `.form-row` | two-column grids collapse to one column |
| `.photo` | moves above the text (`order: -1`) and shrinks to `min(220px, 70%)` |
| `.nav-in` | stops being a fixed 64 px row: `height: auto`, wraps, tighter padding |
| `.nav-links` | smaller type, tighter gap, no auto margin (wraps under the brand) |
| `h1` / `.hero` / `.block` | `2rem` heading, reduced top padding |
| `.card`, `.form` | padding `24px → 18px` |
| `th`, `td` | padding `16px → 12px` |

Because `.nav-links` also has `flex-wrap: wrap` in its base rule, the header can never force horizontal scrolling at any width — the failure mode that earlier produced a 424 px overflow on 320 px screens.

### 2.7 Accessibility by design

- `lang="en"`, `<title>`, and a meta description are present; viewport meta for correct mobile scaling.
- Every section is a real `<section>` with an `id`; headings form a clean outline: **1 × `h1`, 4 × `h2`, 5 × `h3`**.
- The skills matrix is a real `<table>` with `aria-label`, **2 × `scope="col"`** and **4 × `scope="row"`** header cells.
- Form controls use **implicit labels** (`<label>Full Name <input …></label>`), so every control has an accessible name without extra `for`/`id` pairs; `required` marks the three mandatory fields.
- A global `:focus-visible { outline: 2px solid #89b4fa; outline-offset: 2px; }` guarantees visible keyboard focus on every interactive element.
- `html { scroll-padding-top: 72px }` keeps anchor targets clear of the sticky header.
- Decorative framing is done in CSS rather than decorative markup, so screen readers are not read a stream of spacer elements.

### 2.8 Trade-offs accepted to reach 598 / 600 lines

Prettier formats one declaration per line, so stylesheet line count is effectively a *declaration budget* (~2–3 lines of overhead per rule). Reaching 600 lines therefore required cutting structure and decoration, not just whitespace:

- **Dropped:** gradient/glow treatments, all hover variants (focus ring retained), icon badges and colour dots in the table, pill styling inside the skills table (chips remain in the experience list), the definition-list facts block (now one text line), the duplicated footer navigation, the "preferred discussion date" and file-upload fields, Google Fonts (system stack used instead), per-section `aria-labelledby`, and the skip link.
- **Kept:** the full written content, Catppuccin palette, hero photograph, card/table/form components, semantic landmarks, table header semantics, labels, alt text, focus visibility and AA contrast.

---

## 3. Interface Display

### 3.1 Page composition (desktop)

1. **Sticky header** — wordmark `Bagaskoro.dev` (the `.dev` suffix in lavender), then five navigation items: Home, Experience, Skills, Education, and a bordered **Get In Touch** call-to-action.
2. **Hero (`#top`)** — two columns. Left: `Muhammad Bagaskoro` (`h1`, 2.6 rem), the line `$ Software Engineer` in mauve monospace, the summary paragraph (max 54 characters per line for comfortable reading), a location/phone line, and two action buttons: **GitHub** (neutral) and **bagaskoro.moe@gmail.com** (sapphire-highlighted). Right: the square-cropped portrait with a thick surface-coloured frame.
3. **Experience (`#experience`)** — eyebrow `CAREER HISTORY`, heading "Work & Leadership Experience", one card containing the Backend Engineer role (PDBL Team Project) with five `▹` bullets and seven technology chips, followed by an "Organizational Experience" block with two cards (HIMIT PENS, LKMM TD PENS), each carrying a year and organisation line.
4. **Skills (`#skills`)** — eyebrow `COMPETENCIES`, then a single bordered panel holding a four-row, two-column matrix: *Skill Domain* × *Technologies & Methodologies* (Back-end Development, Automation & AI, Database & Distributed Systems, Development Tools).
5. **Education (`#education`)** — eyebrow `ACADEMIC BACKGROUND`, one card naming Politeknik Elektronika Negeri Surabaya (PENS) with the degree and a specialisation note.
6. **Contact (`#contact`)** — eyebrow `GET IN TOUCH`. Left column: "Start a Collaboration" pitch plus four direct channels (email, LinkedIn, GitHub, phone). Right column: the contact form panel.
7. **Footer** — dark crust bar with the note "Designed with Love".

### 3.2 Interaction inventory (static, JS-free)

- Anchored navigation with smooth landing offset (`scroll-padding-top`); the header stays visible while scrolling.
- One visual state per interactive element: the **keyboard focus ring**. Hover styling was intentionally removed in this budget, so pointer and keyboard users get the same, unambiguous affordance.
- Form controls expose native browser validation (`required`, `type="email"`, `type="tel"`) — the only "behaviour" on the page, and it needs no JavaScript.

### 3.3 Mobile presentation (≤ 640 px)

The header wraps into two rows; the portrait sits above the name; every two-column block (hero, contact, form row) becomes a single column; paddings tighten. No horizontal scrolling occurs at any tested width.

---

## 4. User Guide

### 4.1 Opening the page

- **Simplest:** double-click `index.html` — it opens directly in any modern browser; there is no build step and no dependency to install.
- **Via a local server (recommended for verifying the mailto link and clean URLs):**

  ```bash
  cd Homework
  python3 -m http.server 8000 --bind 127.0.0.1
  # then open http://127.0.0.1:8000/index.html
  ```

### 4.2 Navigating the page

1. Use the sticky header links: **Home → Experience → Skills → Education → Get In Touch**. Each one jumps straight to its section (no `scroll-behavior: smooth` — the browser default is used) and lands *below* the header rather than underneath it, thanks to `scroll-padding-top: 72px`.
2. Scroll normally, or press `Tab` to move focus through links and form fields; the blue outline shows exactly where you are. `Shift+Tab` moves backwards.
3. On a phone, the header links wrap onto a second row — all five destinations stay reachable without zooming.

### 4.3 Reading path for a first-time visitor

- **0–5 s:** name, role, one-line pitch, and two direct actions (GitHub / email) in the hero.
- **5–30 s:** the experience card answers "what has he built?" — invoice automation with `.NET`, FastAPI, PaddleOCR and Ollama, plus queueing and caching.
- **30–60 s:** the skills matrix maps domains to technologies; education closes the credibility gap.
- **Then:** the contact section converts interest into action.

### 4.4 Using the contact form

| Field | Type | Required | Notes |
| --- | --- | --- | --- |
| Full Name | text | yes | |
| Email Address | email | yes | browser validates the address format |
| Phone / WhatsApp | tel | no | optional |
| Message / Project Details | textarea (4 rows) | yes | project goals, timelines, questions |

- **Submitting** uses `action="mailto:bagaskoro.moe@gmail.com" method="post" enctype="text/plain"`, so the browser composes an email containing your input and hands it to your default mail client. Review it there, then send.
- **If nothing happens on submit**, no mail client is configured for the system — use the direct channels listed to the left of the form (email, LinkedIn, GitHub, phone) instead.
- **No data is transmitted to or stored on any server by this page**: it is static, with no backend and no JavaScript, so there is no tracking, no cookie, and no database.

### 4.5 Accessibility guide

- **Keyboard-only:** every link and control is reachable with `Tab`; the 2 px blue outline is always visible.
- **Screen reader:** one `h1` names the page, `h2` separates the four subject areas, the skills matrix announces row and column headers, the portrait has the alternative text "Muhammad Bagaskoro", and the form controls are announced with their labels and required state.
- **Low vision:** text colours were verified against their real backgrounds at AA contrast (74 text elements measured, 0 failures); the layout reflows to a single column so text can be enlarged without horizontal scrolling.

### 4.6 Customising the page

| Goal | How |
| --- | --- |
| Change the palette | Replace the hex values listed in §2.2 (note: no `var()`, so use find-and-replace, e.g. `#cba6f7`) |
| Change the portrait | Replace `img/PEN_4350.jpg`; the frame crops it to a square automatically |
| Edit content | All copy is plain HTML in the matching `<section>`; keep the heading levels consistent |
| Re-check the budget | `wc -l index.html` must stay ≤ 600 |
| Re-apply formatting | `npx prettier@3.3.3 --write index.html` |

---

## 5. Quality Evidence (measured)

| Check | Result |
| --- | --- |
| Total LOC (budget 600) | **598** — HTML 257 + CSS 341 (60 rules) |
| Prettier compliance | `npx prettier@3.3.3 --check index.html` → *All matched files use Prettier code style* (Prettier defaults; no `.prettierrc` on this branch) |
| `var()` usage / external stylesheets | 0 / 0 |
| Horizontal overflow | **none** at 320, 360, 375, 414, 640, 768, 1024, 1280, 1600 px (`scrollWidth == clientWidth`) |
| Contrast (AA) | 74 text elements checked, **0 failures** |
| Form accessibility | 4 controls + 1 button, 4 implicit labels, 3 required fields |
| Table semantics | `aria-label` + 2 × `scope="col"` + 4 × `scope="row"` |
| Heading outline | 1 × `h1`, 4 × `h2`, 5 × `h3` |
| Anchor landing | heading lands at 154 px with the header ending at 65 px — not obscured |
| Keyboard focus | `:focus-visible` → `solid 2px rgb(137, 180, 250)` |
| Hero image | `img/PEN_4350.jpg` (4912 × 7360 source) rendered at 300 × 320, square-cropped |

---

## 6. Known Limitations and Suggested Next Steps

1. **Hero image weight** — the source photograph is ~1.26 MB and 4912 px wide while it renders at ~300 px. Resizing it to ~640 px and re-encoding would cut page weight far more than any CSS work. *(Highest-value remaining optimisation.)*
2. **Form is mailto-only** — acceptable for a coursework page; a real deployment would need a backend or form service.
3. **No hover feedback** — a deliberate budget cut; adding a single `:hover` rule per button restores it at the cost of ~8 lines.
4. **Single breakpoint** — tablets (641–1023 px) receive the desktop layout, which is wide but valid; a second breakpoint would improve it at ~15–20 lines.
5. **Not included:** no JavaScript, no favicon, no Open Graph/Twitter preview metadata, no light theme, no skip link, no per-section `aria-labelledby`.
6. **Content still contains masked phone numbers** (`+628****7890`) — confirm this is intended before sharing the page publicly.
7. **Formatting config** — this branch keeps Prettier defaults (printWidth 80), so attribute-heavy tags wrap over several lines. A wider `printWidth: 140` config exists only on the unmerged `refactor/compact-css` branch; applying it here would trim some lines but can shift whitespace inside inline content (for example the `Bagaskoro.dev` wordmark), so it was deliberately left out of this verified build.

---

## 7. Repository Layout and Commands

```
Homework/
├── index.html        # 598 LOC — markup + internal CSS (the whole site)
├── img/PEN_4350.jpg  # hero portrait (only asset)
└── REPORT.md         # this document
```

---

## 8. Glossary

- **Catppuccin Mocha** — a community dark colour scheme; its palette supplies every colour on this page.
- **LOC** — lines of code, counted here with `wc -l` (`598`).
- **Prettier** — opinionated code formatter; it writes one declaration per line, which is why the stylesheet's line count tracks its number of declarations.
- **Implicit label** — a `<label>` that wraps its form control, giving the control an accessible name without `for`/`id` attributes.
- **`:focus-visible`** — CSS selector that shows focus styling for keyboard users while avoiding outlines on mouse clicks.
- **`object-fit: cover`** — CSS property that crops an image to fill its container without distortion.

---

*Report prepared for the `Homework` portfolio rebuild. All figures were measured from the committed artifact on branch `rebuild/600-loc-single-file`.*