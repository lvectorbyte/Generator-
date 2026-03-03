# CLAUDE.md — AI Assistant Guide for Generator-

This file provides context and conventions for AI assistants (Claude, Copilot, etc.)
working in this repository.

---

## Repository Overview

**Project:** Generator- / Prompt Manager Pro
**Author:** janikkmp
**Type:** Single-file HTML web application (no build system, no external JS frameworks)
**Language:** German UI

The application is a **Prompt Manager** — a browser-based SPA for browsing, searching,
filtering, favoriting, and copying AI image generation prompts. All logic, styles, and
data are bundled into a single `.html` file.

---

## Repository Structure

```
Generator-/
├── index.html      # Complete application — CSS, JS, and 5914 prompts in one file
├── README.md       # Project title
└── CLAUDE.md       # This file — AI assistant guide
```

**No build step. No package.json. No bundler.** Open `index.html` directly in a browser.

---

## Application Architecture

### Single-file structure (inside `index.html`)

| Section | Description |
|---------|-------------|
| `<head>` | Google Fonts (Cormorant Garamond, DM Sans) + all CSS |
| `<body>` | Static HTML layout with IDs used by JS |
| `<script>` | `const PROMPTS = [...]` data array followed by all application logic |

### HTML layout IDs

| ID | Element |
|----|---------|
| `#tb` | Top bar (logo, search, controls) |
| `#tabs` | Desktop tab navigation bar |
| `#fb` | Filter bar (dropdowns + hotness slider) |
| `#sb` | Sidebar (category tree) |
| `#ct` | Main content area (prompt cards) |
| `#histbar` | Copy history bar |
| `#botNav` | Mobile bottom navigation |
| `#mo` | Prompt detail modal |
| `#randmod` | Random generator modal |

---

## Data Schema

The prompt dataset is defined as a JavaScript constant at the top of the `<script>` block:

```js
const PROMPTS = [ /* 6354 objects, IDs 1–6354 */ ];
```

**Note:** IDs 1–1590 use JSON format `{"id":N,...}`; IDs 1591–5914 use JS object format `{id:N,...}`. Both are valid JS.

### Prompt object fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | `number` | Unique integer ID |
| `c` | `string` | Category ID (slug) |
| `cn` | `string` | Category display name |
| `l` | `string` | Prompt label / title |
| `t` | `string[]` | Tags array |
| `p` | `string` | Full prompt text (the copyable content) |
| `outfit` | `string` | Outfit descriptor |
| `pose` | `string` | Pose descriptor |
| `face` | `string` | Face descriptor |
| `loc` | `string` | Location descriptor |
| `hot` | `number` | Hotness score 0–100 |

### Prompt categories (`cn` values)

Feed-based: `giannanna Feed 1–5`, `lizakovalenkokoo Feed 1–4`, `vikitoriakpa Feed 1–3`,
`Einzelbilder–Special`

Thematic (original): `Wüste·Marokko·Boho`, `Penthouse·Skyline·Rooftop`, `Bali·Dschungel·Wasserfälle`,
`Skiresort·Alpen·Après-Ski`, `Yacht·Mittelmeer·Segeln`, `Amateur·Candid·Selfie`,
`Fitness·Gym·Sport`, `Glamour·Studio·Portraits`, `Negligé·Chemise·Slip`,
`Leder·Vinyl·Dark`, `Strumpfhosen·Tights·Nylons`, `Kawaii·Harajuku·Sweet`,
`JK-Style·Uniform·Asian`, `Dress·Rock·Kleid`, `Casual·Street·Athleisure`,
`Bikini·Swimwear·Beach`, `Bodysuit·Sheer·Transparent`

Outfit categories (dedicated `c` slugs): `c_bh_set` (BH-Set·Lingerie·Elegant),
`c_bik` (Bikini·Swimwear·Beach), `c_bod` (Bodysuit·Sheer·Transparent),
`c_cas` (Casual·Street·Athleisure), `c_dr` (Dress·Rock·Kleid),
`c_har` (Harness·Dark·Kunstleder), `c_jk` (JK-Style·Uniform·Asian),
`c_kaw` (Kawaii·Harajuku·Sweet), `c_led` (Leder·Vinyl·Dark),
`c_neg` (Negligé·Chemise·Slip), `c_str` (Strumpfhosen·Tights·Nylons),
`c_nerd` (Nerdbrille·Geek·Smart), `c_ahe` (Ahegao·Ausdruck·Intensiv),
`c_nass` (Nass·Wet·Glänzend)

New outfit categories (added session 2): `c_lat` (Latex·Glänzend·Enganliegend),
`c_crop` (Crop-Top·Hot-Pants·Summer), `c_kor` (Korsett·Bustier·Dessous),
`c_push` (Push-up·BH·Lift), `c_spo` (Sporty-Chic·Tennis·Golf),
`c_ove` (Oversize·Cozy·Minimal), `c_zop` (Zöpfe·Pigtails·Sweet)

---

## Key JavaScript Functions

| Function | Purpose |
|----------|---------|
| `render()` | Re-renders the prompt card grid/list from current filter state |
| `getFiltered()` | Returns filtered + sorted subset of `PROMPTS` based on active filters |
| `buildSB()` | Builds the category sidebar from `PROMPTS` data |
| `openModal(id)` | Opens the detail modal for a prompt by ID |
| `copyCard(id, btn)` | Copies prompt text to clipboard; triggers rating toast |
| `cpText(p, btn)` | Copies arbitrary text to clipboard with visual feedback |
| `toggleFav(id, btn)` | Adds/removes a prompt from favorites |
| `blacklistCard(id)` | Moves a prompt to the blacklist |
| `generateRandom()` | Runs the random generator with current modal filters |
| `randomKombo()` | Picks a fully random prompt and shows outfit×pose info |
| `switchTab(t)` | Switches between Browse / Favorites / Blacklist tabs |
| `toggleTheme()` | Toggles dark/light theme and persists to localStorage |
| `exportFiltered()` | Exports currently visible prompts as a downloadable file |
| `showRateToast(id)` | Shows post-copy star rating mini popup (auto-dismisses after 3.5s) |
| `setRating(id, val)` | Stores user's 1–5 star rating for a prompt |

### Application state variables (global JS)

```js
let activeTab      // 'browse' | 'fav' | 'bl'
let activeCategory // current sidebar category filter (string | null)
let viewMode       // 'g' | 'l'
let sortMode       // 'az' | 'hd' | 'hu' | 'rat'
let filters        // { outfit, pose, face, loc, hotMin }
let ratings        // { [promptId]: 1|2|3|4|5 } — user star ratings
```

---

## localStorage

| Key | Contents |
|-----|---------|
| `pmfav` | JSON array of favorited prompt IDs |
| `pmbl` | JSON array of blacklisted prompt IDs |
| `pmhist` | JSON array of last 20 copied prompt texts |
| `pmtheme` | `'dark'` or `'light'` (absent = auto from system preference) |
| `pmrating` | JSON object `{id: 1–5}` — user star ratings per prompt |
| `pmstats` | JSON object — copy statistics (outfits, poses, total, today) |
| `pmcustom` | JSON array — user-created custom prompts |

---

## CSS Theming

All colors are CSS custom properties on `:root`, toggled by a `.light` class on `<body>`:

```css
:root {
  --bg        /* page background */
  --gold      /* accent / highlight color */
  --red       /* danger / hotness indicator */
  /* ... other vars */
}
```

Do not hardcode colors. Use the existing CSS variables.

---

## Feature Inventory

- **Browse tab** — card grid/list view with live search + multi-filter
- **Filters** — Outfit (21 options), Pose (11 options), Face (3 options),
  Location (11 options), Hotness range slider
- **Sort** — A→Z, Hotness ↓, Hotness ↑, Bewertung ↓ (by user star rating)
- **View modes** — Grid and List
- **Category sidebar** — auto-built from `PROMPTS`; collapses on mobile
- **Favorites** — star toggle per card, persisted to `pmfav`
- **Blacklist** — hide prompts; restore, delete, or export from Blacklist tab
- **Copy to clipboard** — button per card with visual confirmation feedback
- **Copy history bar** — last 20 copies, persisted to `pmhist`
- **Detail modal** — full prompt view + similar prompts (prioritised by outfit+pose match)
- **Rating system** — 1–5 stars per card; post-copy rating toast; sort by rating
- **Hotness gradient cards** — hot≥90 → red left-border; hot≥75 → gold left-border
- **Random generator modal** — filter by outfit category, pose, hotness min, count;
  includes ⚡ Zufalls-Kombo for a single random prompt
- **Dark / light theme** — toggle button, persisted to `pmtheme`;
  auto-detects `prefers-color-scheme` when no manual preference is set
- **Export** — downloads currently filtered prompts as a file
- **Keyboard shortcuts**:
  - `/` — focus search input
  - `Esc` — close modal / clear search
  - `R` — open random generator
  - `H` — toggle history bar
  - `S` — toggle sidebar
  - `1` / `2` / `3` — switch tabs
- **Mobile layout** — fixed bottom nav bar; breakpoint at 768px

---

## Development Conventions

### Single-file editing

- All changes happen in `index.html` — CSS, JS, and data in one file
- To add a prompt: append to the `PROMPTS` array in the `<script>` block
- To change a style: find or add a CSS variable or rule in the `<head>` block
- To add a feature: add JS logic in the `<script>` block and update HTML if needed
- Keep the existing minified/compact style for PROMPTS array entries; do not reformat them

### General Principles

1. **Keep it simple** — no build tools, no frameworks, no npm
2. **No dead code** — delete unused functions and rules rather than commenting them out
3. **No backwards-compatibility hacks** — change code directly
4. **Minimal error handling** — only validate at true boundaries (user input, clipboard API)
5. **Follow surrounding style** — match the existing code style in whatever section you edit
6. **German UI** — all user-visible strings must be in German; do not introduce English labels

---

## Git Workflow

### Branches

| Branch | Purpose |
|--------|---------|
| `main` / `master` | Stable, production-ready code |
| `claude/<description>-<id>` | AI-assisted feature branches |
| `feature/<description>` | Human feature branches |

### Commit Messages

Write short, imperative commit messages (≤72 chars, imperative mood):

```
Add hotness filter to random generator
Fix sidebar not closing on mobile after category select
Update PROMPTS with 10 new Bali entries
```

Reference issues with `#<number>` when applicable.

### Push Convention

```bash
git push -u origin <branch-name>
```

---

## Working with This Repository

### Starting a Session

1. `git branch` — confirm you are on the correct branch
2. `git pull origin <branch>` — get latest changes
3. Open `index.html` in a browser to verify current state before editing

### Making Changes

1. Edit `index.html` directly — no compile step needed
2. Test by refreshing the browser
3. Prefer targeted edits; avoid reformatting unrelated sections

### Finishing a Session

1. Stage by filename: `git add index.html CLAUDE.md`
2. Commit with a descriptive message
3. Push: `git push -u origin <branch-name>`

---

## What to Avoid

- **Do not push to `main`/`master` directly** — open a PR
- **Do not force-push** unless explicitly instructed
- **Do not commit secrets** (API keys, `.env` files, credentials)
- **Do not skip pre-commit hooks** (`--no-verify`)
- **Do not add features or refactors** beyond what was explicitly requested
- **Do not introduce English UI strings** — the app is German-language
- **Do not split into multiple files** — the single-file architecture is intentional
- **Do not reformat the PROMPTS array** — it is intentionally compact

---

## Updating This File

Update `CLAUDE.md` whenever:

- New features are added to the application
- The PROMPTS dataset schema changes
- New localStorage keys are introduced
- The directory structure changes (e.g., assets folder added)
- New keyboard shortcuts or filter options are added

The goal is to keep this file accurate as a first-read for any AI assistant.

---

## Outfit Matrix

**23 outfit values** × **11 pose values** = 253 combos, each with ≥ 20 hot>90 prompts.

| Outfits (23) | Poses (11) |
|---|---|
| BH-Set, Bikini, Bodysuit, Casual/Sport | Stehend, Liegend, Sitzend, Kniend |
| Dress/Rock, Harness, JK-Style, Kawaii | Selfie, Close-Up, Rückenansicht, Duo |
| Leder/Vinyl, Negligé, Strumpfhosen, Nerdbrille | Hängend/Baumeln, Im Wasser, Auf allen Vieren |
| Ahegao, Nass, Latex/Glänzend, Crop-Top/Hot-Pants | |
| Korsett/Bustier, Push-up/BH, Sporty-Chic/Tennis | |
| Oversize/Cozy, Zöpfe/Pigtails | |
| Overknee/Schuhe, Escort-Stil | |

**Data quality rule:** All prompts begin with `"22 year old woman, mature adult features,"`.
No age-ambiguous terms. All hot>90 prompts have `hot: 92–99`.
