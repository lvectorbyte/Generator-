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
├── index.html      # Complete application — CSS, JS, and 7190 prompts in one file
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
| `#kombmod` | Prompt combinator + mixer modal |
| `#vp-panel` | Video prompt overlay panel |
| `#rate-toast` | Rating toast (shown after copy) |
| `#fs-panel` | Filter-set save/load panel |
| `#login-screen` | Password protection screen |

---

## Data Schema

The prompt dataset is defined as a JavaScript constant at the top of the `<script>` block:

```js
const PROMPTS = [ /* 7190 objects */ ];
```

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

Thematic: `Wüste·Marokko·Boho`, `Penthouse·Skyline·Rooftop`, `Bali·Dschungel·Wasserfälle`,
`Skiresort·Alpen·Après-Ski`, `Yacht·Mittelmeer·Segeln`, `Amateur·Candid·Selfie`,
`Fitness·Gym·Sport`, `Glamour·Studio·Portraits`, `Negligé·Chemise·Slip`,
`Leder·Vinyl·Dark`, `Strumpfhosen·Tights·Nylons`, `Kawaii·Harajuku·Sweet`,
`JK-Style·Uniform·Asian`, `Dress·Rock·Kleid`, `Casual·Street·Athleisure`,
`Bikini·Swimwear·Beach`, `Bodysuit·Sheer·Transparent`

### Outfit categories (28 total, stored in `OUTFIT_CATS_LIST`)

Original 14: BH-Set, Bikini, Bodysuit, Casual/Sport, Dress/Rock, Harness, JK-Style,
Kawaii, Leder/Vinyl, Negligé, Strumpfhosen, Nerdbrille, Ahegao, Nass

New 14: Latex/Glänzend (`c_lat`), Crop-Top/Hot-Pants (`c_crop`), Korsett/Bustier (`c_kor`),
Push-up/BH (`c_push`), Sporty-Chic/Tennis (`c_spo`), Oversize/Cozy (`c_ove`),
Zöpfe/Pigtails (`c_zop`), Overknee/Schuhe (`c_oks`), Escort-Stil (`c_esc`),
Wet T-Shirt (`c_wet`), Club-Dress/Party (`c_club`), Braut/Dessous (`c_braut`),
Cosplay/Anime (`c_cos`), Skinny (`c_skn`)

### Pose categories (14 total)

Original 8: Stehend, Liegend, Sitzend, Kniend, Selfie, Close-Up, Rückenansicht, Duo

New 6: Hängend/Baumeln, Im Wasser, Auf allen Vieren, Spiegel-Pose, Im Stuhl/Sessel, Treppenaufgang

---

## Key JavaScript Functions

| Function | Purpose |
|----------|---------|
| `render()` | Re-renders the prompt card grid/list from current filter state |
| `getFiltered()` | Returns filtered + sorted subset of `PROMPTS` based on active filters |
| `buildSB()` | Builds the category sidebar from `PROMPTS` data |
| `openModal(id)` | Opens the detail modal for a prompt by ID |
| `copyCard(id, btn)` | Copies prompt text to clipboard; updates history |
| `cpText(p, btn)` | Copies arbitrary text to clipboard with visual feedback |
| `toggleFav(id, btn)` | Adds/removes a prompt from favorites |
| `blacklistCard(id)` | Moves a prompt to the blacklist |
| `generateRandom()` | Runs the random generator with current modal filters |
| `switchTab(t)` | Switches between Browse / Favorites / Blacklist tabs |
| `toggleTheme()` | Toggles dark/light theme and persists to localStorage |
| `exportFiltered()` | Exports currently visible prompts as a downloadable file |
| `showRateToast(id)` | Shows floating star-rating toast after copying a prompt |
| `initSwipe()` | Sets up swipe-right=fav / swipe-left=blacklist on touch devices |
| `showVideoPrompt(id)` | Shows structured 15s video prompt JSON in `#vp-panel` |
| `getVideoPrompt(p)` | Builds Runway/Kling video prompt JSON from a prompt object |
| `doMix()` | Generates a mixed prompt from outfit+pose selection in Mixer |
| `fillMixSelects()` | Populates Mixer outfit/pose dropdowns with all 28/14 options |
| `toggleFsPanel()` | Shows/hides the filter-set panel |
| `saveFilterSet(name)` | Saves current filter state to `pmfilters` localStorage |
| `loadFilterSet(name)` | Restores a saved filter state and re-renders |
| `checkPw()` | Validates entered password against `_k` hash; stores `pmauth` |

### Application state variables (global JS)

```js
let activeTab      // 'browse' | 'fav' | 'bl'
let activeCategory // current sidebar category filter (string | null)
let viewMode       // 'g' (grid) | 'l' (list)
let sortMode       // 'az' | 'hd' | 'hu' | 'rat'
// Filter state is DOM-based — read from #fo, #fp, #ff, #fl2, #hmin directly
```

---

## localStorage

| Key | Contents |
|-----|---------|
| `pmfav` | JSON array of favorited prompt IDs |
| `pmbl` | JSON array of blacklisted prompt IDs |
| `pmhist` | JSON array of last 20 copied prompt IDs |
| `pmtheme` | `'dark'` or `'light'` |
| `pmrating` | JSON object mapping prompt ID → star rating (1–5) |
| `pmstats` | JSON object with copy statistics |
| `pmcustom` | JSON array of user-created custom prompts |
| `pmfilters` | JSON object mapping set name → saved filter state |
| `pmauth` | Auth token (btoa of password); presence unlocks the app |

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
- **Filters** — Outfit (28 options), Pose (14 options), Face (3 options),
  Location (11 options), Hotness range slider
- **Sort** — A→Z, Hotness ↓, Hotness ↑, Rating ⭐
- **View modes** — Grid and List
- **Category sidebar** — auto-built from `PROMPTS`; collapses on mobile
- **Favorites** — star toggle per card, persisted to `pmfav`
- **Blacklist** — hide prompts; restore, delete, or export from Blacklist tab
- **Copy to clipboard** — button per card with visual confirmation feedback
- **Copy history bar** — last 20 copies, persisted to `pmhist`
- **Detail modal** — full prompt view + similar prompts section
- **Random generator modal** — filter by category, outfit, hotness min, count
- **Dark / light theme** — toggle button, persisted to `pmtheme`
- **Export** — downloads currently filtered prompts as a file
- **Keyboard shortcuts**:
  - `/` — focus search input
  - `Esc` — close modal / clear search
  - `R` — open random generator
  - `H` — toggle history bar
  - `S` — toggle sidebar
  - `1` / `2` / `3` — switch tabs
- **Mobile layout** — fixed bottom nav bar; breakpoint at 768px
- **Swipe gestures** — swipe right on card = Favorit, swipe left = Blacklist
- **Rating** — 1–5 star rating per prompt; shown as toast after copy; sort by rating
- **Video prompts** — 🎬 button on every card opens structured 15s Runway/Kling JSON prompt
- **Filter sets** — 💾 button saves/loads named filter presets (stored in `pmfilters`)
- **Mixer** — inside Kombinator modal; picks prompts by outfit+pose and mixes them
- **Password** — `Fussball123...` protects the app; stored as btoa hash in `pmauth`
- **MJ format** — outputs `--style raw --v 6.1 --stylize 750` (updated from v6)

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
