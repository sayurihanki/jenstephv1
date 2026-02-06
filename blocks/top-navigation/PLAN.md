# Masthead Block – Planning

Plan for a **top-of-page** block for the Adobe Commerce storefront: promo/utility strip, optional main nav (including mega menu), optional image banner, and a single “primary nav source” so either the header or this block owns the main nav—never both. Aligns with `.cursor/rules/adobe-commerce-blocks.mdc` and existing block patterns (header, store-locator, hero-cta).

**Block name in this plan:** **masthead**. Folder and files use this name unless you choose one of the alternatives below.

---

## 0. Block Naming – 5 Recommendations

Pick one for the block (folder, `block-name.js`, `_block-name.json`, DA.live table row):

| Name | Vibe | Notes |
|------|------|--------|
| **masthead** | Professional, editorial | Classic term for the top strip of a page; clear and recognizable. **Used in this plan.** |
| **apex** | Short, bold | “Top” in one word; distinctive and easy to type. |
| **ridge** | Memorable, visual | Like a ridge at the top of the page; single word. |
| **crest** | Elegant | Top of a wave/hill; implies “peak” content at the top. |
| **helm** | Navigation-focused | Steering/navigation metaphor; fits when this block drives main nav. |

Use one name consistently: `blocks/<name>/`, `<name>.js`, `<name>.css`, `_<name>.json`, and the DA.live block table row (e.g. `masthead`).

---

## 1. Relationship to Current Header

| Current setup | This block’s role |
|---------------|-------------------|
| **header** block: brand (logo) + nav fragment + tools (wishlist, mini-cart, search, auth) | **Masthead**: strip *above* header—promo, utility links, optional image banner, and *optionally* the main nav (when `nav-source` = masthead). |
| Nav from fragment (`/nav`) in header | When masthead is primary nav source, masthead shows main nav (fragment/table); header shows **brand + tools only** (no nav). |
| Single place for “main nav” | **Primary nav source** is chosen once (document or section metadata): `header` or `masthead`. Only that block shows main nav; the other does not. |

**Logo:** Stays in the **header** only. This block does not include site logo.

**Placement:** One section with the masthead block above a section with the header. Same layout whether masthead is promo-only or primary nav.

---

## 2. Primary Nav Source (DA.live Definable)

One setting controls which block owns the **main navigation**; the other never shows it. Definable in DA.live.

### Metadata key and values

- **Key:** `nav-source` (document metadata) or `data-nav-source` (section metadata; DA.live double-prefix).
- **Values:** `header` | `masthead` (or the block name you chose, e.g. `apex`).
- **Default:** `header` (current behavior: header shows nav, masthead is promo/utility only).

### Behavior

- **`nav-source === 'header'`**  
  - Header: loads and shows nav fragment (current behavior).  
  - Masthead: shows only non-nav variants (minimal, promo, utility, banner)—no main nav, no mega menu as primary nav.
- **`nav-source === 'masthead'`**  
  - Masthead: shows main nav (from fragment/table), including mega if configured.  
  - Header: does **not** load or show nav; shows brand + tools only (cart, search, auth, wishlist).

Both blocks read the same source (document or section metadata); normalize value and default to `header` so invalid/typos don’t show both navs.

### Where to define in DA.live

- **Section metadata (recommended if masthead + header share a section):**  
  Add a Section Metadata table for that section. Row:  
  `data-nav-source` | `header` or `data-nav-source` | `masthead`  
  Code reads `section?.dataset.dataNavSource` (double-prefix).
- **Document metadata (if available):**  
  Add a row: `nav-source` | `header` or `nav-source` | `masthead`.  
  Code reads via `getMetadata('nav-source')`.

Document in README: “Set **Primary nav source** to `header` (default) or `masthead`. Only one block shows the main navigation.”

---

## 3. Block Structure (Per Your Rules)

- `masthead.js` – decorate, config, fragment/table, nav-source check, fragment load, link building
- `masthead.css` – layout, variants, design tokens only
- `README.md` – Overview, Integration, Configuration, Behavior, Accessibility, Troubleshooting
- `_masthead.json` – DA.live block definition

(Replace `masthead` with chosen name from §0.)

---

## 4. Configuration (Block + Section, Double-Prefix)

Support config from block and section with **double-prefix** for section metadata:

| Config key | Type | Default | Description |
|------------|------|---------|-------------|
| `data-sticky` | boolean | `false` | Stick to top on scroll |
| `data-variant` | string | `minimal` | `minimal` \| `promo` \| `utility` \| `mega` \| `banner` (see §6) |
| `data-align` | string | `center` | Content alignment: `left`, `center`, `right` |
| `data-max-width` | string | `1248` | Max width in px (match header) |
| `data-show-on-scroll` | string | `none` | `none` \| `show` \| `hide` – show/hide bar on scroll direction |

Normalize with helpers (e.g. `normalizeAlign(value, 'center')`) and apply to `block.dataset.*` for CSS.

---

## 5. DA.live Integration

- **Fragment:** Metadata e.g. `top-nav` or `masthead-nav` → path to fragment. Block loads and decorates. When `nav-source === 'masthead'`, this fragment can be the main nav (same structure as header nav if desired).
- **Block table:** Row 1 = block name (e.g. `masthead`). Config rows: Variant, Sticky, Align, etc. Data rows: links (Label | URL) or banner (Image URL | Link URL). Optional columns: Icon, Open in New Tab.
- **Image banner:** Config row `Banner Image` | URL and `Banner Link` | URL, or dedicated data rows for one full-width banner image + link.
- **Hybrid:** Table for config + link list; optional fragment for richer nav/promo content.

---

## 6. Variants (Including Image Banner)

| Variant | Purpose | Content |
|--------|---------|---------|
| **minimal** | Simple link bar | Links only (e.g. Store Locator, Help). |
| **promo** | Announcement strip | One line of text + optional CTA; dismissible (localStorage/session); `aria-live`. |
| **utility** | Utility link bar | Store Locator, Help, Contact, Order Status, optional language/region. |
| **mega** | Category mega menu | First row = categories; hover/focus opens large dropdown (columns, optional images). Overlay, Escape, focus trap like header. Only when `nav-source === 'masthead'`. |
| **banner** | Optional image strip | One full-width (or container-width) image + optional link. Uses `createOptimizedPicture`; one config row or table row (Image URL | Link URL). |

Logo: not in this block; header remains the single place for site logo.

---

## 7. Features (Must / Should / Nice-to-Have)

### Must-have

- Responsive layout (single row desktop; hamburger or horizontal scroll on mobile; mobile-first).
- Accessibility: keyboard (Tab, Enter, Escape), ARIA, focus trap in mobile menu, skip-link consideration.
- Design tokens for spacing, color, typography.
- Commerce-aware: hide or simplify on checkout.
- Fragment or table content; no hardcoded nav.
- **Primary nav source:** read `nav-source` / `data-nav-source`; show main nav only when `nav-source === 'masthead'`; header shows nav only when `nav-source === 'header'`.

### Should-have

- Promo strip (text + CTA, dismissible).
- Utility links variant.
- Sticky option (`data-sticky`); only one sticky bar when used with header.
- Event bus for `cart/data`, `authenticated` if showing cart/account in this block.
- **Image banner:** optional `banner` variant or config row (image + link).

### Nice-to-have

- Mega menu (when masthead is primary nav).
- Show/hide on scroll (`data-show-on-scroll`); respect `prefers-reduced-motion`.
- Multiple rows (e.g. promo row + utility row).
- Analytics (ACDL / storefront events) for link clicks.

---

## 8. Behavior Patterns

- **Nav source:** Both masthead and header read `nav-source` (document or section). Only the selected block renders main nav; the other omits or hides it.
- **Page context:** Checkout: hide or minimal. Account pages: optional Account/Sign out in masthead when using event bus.
- **Loading:** Skeleton or placeholder for fragment; fallback to table-only on error.
- **Mobile:** Collapse to menu or horizontal scroll; avoid stacking too much above header hamburger.

---

## 9. Accessibility Checklist

- Keyboard navigation for all interactive elements.
- ARIA (role, aria-expanded, aria-controls, aria-label).
- `prefers-reduced-motion: reduce` – no scroll-based show/hide animation when user prefers reduced motion.
- Color contrast for custom promo/CTA; validate with design tokens.

---

## 10. Implementation Order

1. **Scaffold** – `masthead.js`, `masthead.css`, `_masthead.json`, `README.md`; register block in scripts/block loader.
2. **Nav source** – Read `nav-source` / `data-nav-source` (document or section); normalize; header block: when `nav-source === 'masthead'`, skip loading/rendering nav; masthead: when `nav-source === 'masthead'`, render nav from table/fragment.
3. **Config + table** – `readBlockConfig()`, double-prefix, normalize; render links from table.
4. **Fragment** – Optional: load fragment when metadata set; merge or replace table content.
5. **Variants** – CSS for `minimal`, `utility`, then `promo` (text + CTA + dismiss), then `banner` (image + link with `createOptimizedPicture`), then `mega` (when masthead is primary nav).
6. **Sticky** – `data-sticky`; ensure only one sticky bar with header.
7. **Responsive + a11y** – Mobile layout, keyboard, ARIA, reduced motion.
8. **Commerce** – Hide on checkout; optional event bus.
9. **Polish** – Scroll show/hide, analytics.

---

## 11. Files to Add / Touch

| File | Purpose |
|------|--------|
| `blocks/masthead/masthead.js` | Decorate, config, nav-source, fragment, links, variants |
| `blocks/masthead/masthead.css` | Layout, variants, tokens |
| `blocks/masthead/README.md` | Full doc; document nav-source and Section Metadata |
| `blocks/masthead/_masthead.json` | DA.live block definition |
| `scripts/scripts.js` (or block loader) | Register masthead block |
| **header/header.js** | When `nav-source === 'masthead'`, do not load/render nav; keep brand + tools only |
| Document or section metadata | `nav-source` or `data-nav-source` in DA.live |

Use chosen block name (e.g. `apex`, `ridge`) in folder and filenames if not using `masthead`.

---

## 12. Conformance (Your Rules + Adobe Commerce Storefront)

Same as before: block structure (JS, CSS, README, JSON); double-prefix and config merge; data-attribute variants and design tokens; specificity order and modern `rgb()`; image optimization for banner/mega; a11y and loading states; README with Section Metadata note; ESLint/Stylelint; progressive enhancement and separation of concerns. Decorator `export default async function decorate(block)`; block discovery by name; DA.live table and section/document metadata for nav-source and config.

---

## 13. Summary

- **Name:** Choose one of: **masthead**, **apex**, **ridge**, **crest**, **helm**; use it for folder, files, and DA.live.
- **Placement:** One section above the header; logo stays in header.
- **Primary nav source:** One setting (`nav-source` or `data-nav-source`) in DA.live (document or section metadata): `header` or `masthead`. Only that block shows main nav; never both.
- **Variants:** minimal, promo, utility, mega, banner (optional image strip).
- **Content:** DA.live table and/or fragment; config from block + section (double-prefix).
- **Conventions:** Same as your other blocks and Adobe Commerce Storefront patterns.
