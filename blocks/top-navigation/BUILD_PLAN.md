# Masthead Block – Build Plan

Step-by-step implementation plan. Block name used here: **masthead** (rename folder from `top-navigation` to `masthead` when you start, or use your chosen name and replace all references).

**Discovery:** Blocks are loaded by class name: `div.section > div > div` with first class = block name. No registration in `scripts.js` needed—create `blocks/masthead/masthead.js` (and CSS, JSON) and the block loads when a section contains a block table with first cell `masthead`.

---

## Phase 0: Setup (before coding)

- [ ] **Pick block name** (masthead | apex | ridge | crest | helm). Use it for folder, files, DA.live.
- [ ] **Rename folder** from `blocks/top-navigation/` to `blocks/masthead/` (or chosen name).
- [ ] **Confirm placement:** Masthead will be a normal block in a section; authors add one section with masthead above the section that contains the header (header is loaded into `<header>` by `loadHeader()` in `loadLazy`).

---

## Phase 1: Scaffold & config (no nav yet)

**Goal:** Block loads, reads config, renders a simple wrapper. No nav source logic yet.

### 1.1 Files to create

| File | Purpose |
|------|--------|
| `blocks/masthead/masthead.js` | `export default async function decorate(block)` – clear block content, read config, apply to `block.dataset`, build minimal DOM (e.g. one wrapper div). |
| `blocks/masthead/masthead.css` | Base layout: bar full width, max-width from variable, design tokens only. |
| `blocks/masthead/_masthead.json` | DA.live: block name `masthead`, rows/columns as needed (see hero-cta or store-locator `_*.json`). |
| `blocks/masthead/README.md` | Stub: Overview, “Configuration” section with table of data attributes. |

### 1.2 Config in masthead.js

- Use `readBlockConfig(block)` for table config (from `scripts/aem.js` or commerce.js).
- Use `block.closest('.section')` for section; read section metadata with **double-prefix**: `section?.dataset.dataSticky`, `section?.dataset.dataVariant`, `section?.dataset.dataAlign`, etc. (DA.live turns keys into `data-*` attributes with double prefix on section.)
- Merge: block config first, then section (double-prefix), then defaults.
- Normalize: `normalizeVariant(value, 'minimal')`, `normalizeAlign(value, 'center')` (allow `minimal` | `promo` | `utility` | `mega` | `banner`).
- Apply final config to `block.dataset` (e.g. `block.dataset.variant = config.variant`) so CSS can use `[data-variant='minimal']` etc.

### 1.3 Deliverable

- [ ] Block appears when a section has a table with first cell `masthead`.
- [ ] No errors in console; block shows empty or placeholder bar.
- [ ] `npm run lint` passes for new files.

---

## Phase 2: Nav source (metadata + header behavior)

**Goal:** One place defines who owns main nav; header and masthead both respect it.

### 2.1 Read nav source

- In **masthead.js**: at start of `decorate()`, read nav source:
  - `getMetadata('nav-source')` (document metadata), or
  - `section?.dataset.dataNavSource` (section metadata; double-prefix).
- Normalize: `normalizeNavSource(value, 'header')` → `'header'` | `'masthead'`. Invalid/empty → `'header'`.
- Store in variable e.g. `const isPrimaryNav = navSource === 'masthead';`.

### 2.2 Header: conditional nav

- In **blocks/header/header.js**: before loading the nav fragment, read same nav source (getMetadata + section fallback; section is `block.closest('.section')` but header block is inside `<header>`, so you may need to read document metadata only, or first section in main that has `data-nav-source`—decide convention).
- If `navSource === 'masthead'`: do **not** load nav fragment; do **not** render nav sections (brand + tools only). Skip the block that builds nav from fragment; still render brand, wishlist, mini-cart, search, auth.
- If `navSource === 'header'` or missing: current behavior (load nav, full header).

### 2.3 Masthead: show nav only when primary

- In masthead.js: when building content, if `!isPrimaryNav`, only render non-nav variants (minimal, promo, utility, banner)—no mega, no main nav list from fragment.
- When `isPrimaryNav`, later phases will add fragment/table-driven nav and mega.

### 2.4 Deliverable

- [ ] With no metadata or `nav-source` = header: header shows nav as today; masthead shows no main nav.
- [ ] With `nav-source` = masthead (in document or section metadata): header shows brand + tools only; masthead will show main nav once Phase 3/4 are done.
- [ ] README updated: “Primary nav source” and DA.live instructions (Section Metadata: `data-nav-source` | header | masthead).

---

## Phase 3: Minimal & utility variants (table-driven links)

**Goal:** Render links from block table; support `minimal` and `utility` variants.

### 3.1 Parse link rows

- From `readBlockConfig(block)` or by iterating block rows: detect data rows (e.g. two columns: label, url). Skip config rows (Variant, Sticky, Align, etc.).
- Build list of `{ label, url, external }` (optional “Open in New Tab” column).
- Optional: support fragment path from metadata (e.g. `getMetadata('masthead-nav')`) and load via `loadFragment` from `../fragment/fragment.js`; if present, use fragment content for links instead of or in addition to table.

### 3.2 DOM for minimal/utility

- Create wrapper with class e.g. `masthead__inner`; max-width from config.
- Create `<nav>` or `<ul>` with links; use `rootLink()` from commerce.js for hrefs.
- Apply `data-variant` and `data-align` on block for CSS (alignment, spacing).
- When `!isPrimaryNav`, only render this link list (no nav sections from fragment for mega).

### 3.3 CSS

- `.masthead[data-variant='minimal']`, `.masthead[data-variant='utility']`: layout, typography from design tokens.
- `[data-align='left']`, `[data-align='center']`, `[data-align='right']`: justify content.
- Mobile: horizontal scroll or hamburger (single row of links or collapse).

### 3.4 Deliverable

- [ ] DA.live table with rows like `Store Locator | /store-locator` produces working links.
- [ ] Variant minimal vs utility changes style only (both are link bars).
- [ ] Responsive: usable on mobile (scroll or menu).
- [ ] Accessibility: keyboard, focus, ARIA labels on nav/links.

---

## Phase 4: Promo variant

**Goal:** Variant `promo`: one line of text + optional CTA button; dismissible.

### 4.1 Config / table

- Promo text and CTA from block table (e.g. one row: “Free shipping over $50” | “Shop now” with URL in next row or column) or from first two cells.
- Optional config: “Dismissible” = true; store dismiss in sessionStorage or localStorage (key e.g. `masthead-promo-dismissed`).

### 4.2 DOM

- Bar with class for promo; inner: text span + optional `<a>` or button for CTA. If dismissible: close button with `aria-label="Dismiss"`.
- Use `aria-live="polite"` for the announcement text.

### 4.3 JS

- On dismiss: set storage, hide bar (remove from DOM or add class), so it doesn’t show again for session/page.

### 4.4 CSS

- `.masthead[data-variant='promo']`: layout for text + CTA + dismiss; design tokens; contrast for text/CTA.

### 4.5 Deliverable

- [ ] Promo variant shows text + CTA; dismiss hides it and respects storage.
- [ ] Accessibility: aria-live, keyboard, focus.

---

## Phase 5: Banner variant (image strip)

**Goal:** Optional full-width (or container-width) image + optional link.

### 5.1 Config

- Block table or config row: “Banner Image” | URL, “Banner Link” | URL (optional).
- Or one data row: image URL | link URL.

### 5.2 DOM & image

- Use `createOptimizedPicture(src, alt, eager, breakpoints)` from `scripts/aem.js` for the banner image. Eager load if above the fold.
- Wrap in `<a>` if link URL present; else plain picture.
- Apply only when `data-variant='banner'` (or separate “banner” row so it can combine with another variant if you support multiple rows).

### 5.3 CSS

- `.masthead[data-variant='banner']`: full width or max-width; object-fit; design tokens.

### 5.4 Deliverable

- [ ] Banner variant shows optimized image; optional link works.
- [ ] No hardcoded dimensions; use tokens and responsive behavior.

---

## Phase 6: Fragment-driven nav (when masthead is primary)

**Goal:** When `nav-source === 'masthead'`, load nav from fragment (e.g. same `/nav` or dedicated path) and render as main nav in masthead.

### 6.1 Fragment load

- Metadata: e.g. `nav` or `masthead-nav` → path (reuse `/nav` or new path).
- In masthead, when `isPrimaryNav`: `loadFragment(path)` (from `../fragment/fragment.js`). Decorate fragment content like header: brand area skipped (header has it); turn list into nav structure (ul/li, add classes for nav-sections).

### 6.2 Structure

- Reuse patterns from header: nav sections with dropdown triggers (`.nav-drop`), submenus. You can reuse overlay and keyboard logic from header.js (extract or copy) for mega in Phase 7.
- Desktop: horizontal nav; mobile: hamburger + expandable list.

### 6.3 Deliverable

- [ ] With `nav-source` = masthead and fragment path set, masthead shows full nav from fragment.
- [ ] Header still shows no nav (brand + tools only).
- [ ] Loading: skeleton or placeholder while fragment loads; on error, fallback to table links or empty.

---

## Phase 7: Mega menu (when masthead is primary)

**Goal:** Variant `mega`: category items open large dropdowns (columns, optional images). Reuse overlay, Escape, focus from header.

### 7.1 Structure

- First row of nav = top-level categories; each can have children (from fragment structure). On hover/focus, show mega panel: multiple columns of links, optional images per column or per item.
- Use overlay div (like header); close on Escape; focus trap when open; `aria-expanded`, `aria-controls`, `aria-label`.

### 7.2 Content

- From fragment DOM: parse nested lists; optionally from block table (e.g. “Mega” rows with image URL + link + label). Use `createOptimizedPicture` for any images in mega panels.

### 7.3 CSS

- `.masthead[data-variant='mega']`: desktop dropdown panels (position, z-index, design tokens); mobile accordion or stacked list.
- Respect `prefers-reduced-motion` for open/close.

### 7.4 Deliverable

- [ ] Mega variant works when masthead is primary nav; dropdowns open/close; keyboard and Escape work.
- [ ] No duplicate overlay with header (only one open at a time).

---

## Phase 8: Sticky & show/hide on scroll

**Goal:** `data-sticky="true"` and `data-show-on-scroll="hide"` | `"show"`.

### 8.1 Sticky

- When `block.dataset.sticky === 'true'`: apply `position: sticky; top: 0` to bar (or wrapper). Ensure only one sticky bar: if both masthead and header exist, either masthead or header is sticky, not both (config or convention: e.g. when masthead sticky, header not sticky).

### 8.2 Show/hide on scroll

- When `data-show-on-scroll="hide"`: hide bar on scroll down, show on scroll up (translate or visibility). When `"show"`: reverse or same as typical “show on scroll up” pattern.
- In JS: listen to scroll, track direction; add/remove class. In CSS: `@media (prefers-reduced-motion: reduce)` – disable animation (no transform/transition).

### 8.3 Deliverable

- [ ] Sticky works; no double sticky with header.
- [ ] Scroll behavior works; reduced motion respected.

---

## Phase 9: Commerce & polish

**Goal:** Checkout hide, event bus (optional), analytics (optional), README and lint.

### 9.1 Checkout

- In masthead.js: if `window.location.pathname === '/checkout'` (or your checkout path), hide block or render minimal (e.g. empty or single line). Use CSS or skip render.

### 9.2 Event bus (optional)

- If showing cart count or “Account” in masthead: `events.on('cart/data', ...)` and `events.on('authenticated', ...)` from `@dropins/tools/event-bus.js`; update DOM. Same pattern as header.

### 9.3 Analytics (optional)

- For link clicks in masthead: use ACDL / storefront events (same as rest of site). Document in README.

### 9.4 README & Section Metadata

- README: Overview, Integration (block table + Section Metadata), Configuration (all data attributes + nav-source), Behavior, Accessibility, Troubleshooting.
- Document: “Configuration via Section Metadata” table; “Section Metadata must be directly above the block” (if that’s the convention).
- Document nav-source: `data-nav-source` | header | masthead in Section Metadata; or document metadata `nav-source`.

### 9.5 Lint & conformance

- [ ] `npm run lint` and `npm run lint:css` pass.
- [ ] No hardcoded colors/spacing; design tokens only. Data attributes for variants. Double-prefix for section metadata.

---

## Dependency order

```
Phase 0 → Phase 1 → Phase 2 → Phase 3 → Phase 4 → Phase 5
                ↓
            Phase 6 → Phase 7
                ↓
            Phase 8 → Phase 9
```

- Phase 2 (nav source) can start after 1.1; header change and masthead “isPrimaryNav” gate are required before 6 and 7.
- Phases 3, 4, 5 (minimal, utility, promo, banner) can be parallel after Phase 2.
- Phase 6 (fragment nav) and 7 (mega) depend on Phase 2.
- Phase 8 and 9 can follow once 3–7 are done.

---

## Quick reference: imports and helpers

- **Config:** `readBlockConfig(block)` – aem.js or commerce.js.
- **Metadata:** `getMetadata(name)` – aem.js.
- **Fragment:** `loadFragment(path)` – `../fragment/fragment.js`.
- **Links:** `rootLink(path)` – commerce.js.
- **Image:** `createOptimizedPicture(src, alt, eager, breakpoints)` – aem.js.
- **Section:** `block.closest('.section')`; section metadata: `section.dataset.dataNavSource` (double-prefix).
- **Header overlay/keyboard:** Reuse or copy from `blocks/header/header.js` (toggleAllNavSections, closeOnEscape, overlay, focus trap).

Use this build plan as the execution checklist; tick off items as you complete them.
