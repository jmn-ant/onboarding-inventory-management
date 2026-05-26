---
name: saas-ui-redesign
description: Redesign this Vue 3 frontend into a modern SaaS-style interface — replace the top navigation bar with a left vertical navigation sidebar, consolidate scattered styles into a design-token system, enforce consistent spacing, and apply a polished professional look. Use this skill when asked to modernize the UI, convert the top nav to a sidebar, restyle the app SaaS-style, or improve visual consistency of the frontend.
---

# SaaS UI Redesign (Sidebar Navigation)

This skill drives a full redesign of the Factory Inventory Management frontend from its current
**top-navigation** layout into a modern **SaaS-style left sidebar** layout, with a consolidated
design-token system, consistent spacing, and a polished professional finish.

Follow the workflow **in order**. It is procedural: each step has concrete actions, the exact tool
or subagent to use, and a checkpoint before moving on.

---

## Critical rules (read before touching anything)

These come from the project's `CLAUDE.md` and are non-negotiable:

1. **Delegate every `.vue` change to the `vue-expert` subagent.** ANY time you create or significantly
   modify a `.vue` file (`App.vue`, a new `Sidebar.vue`, any view), you MUST use the Task tool with
   `subagent_type: vue-expert`. Do not hand-edit `.vue` files yourself. Give the subagent the exact
   markup/CSS from this skill plus the integration context.
2. **Use Playwright MCP tools** (`mcp__playwright__*`) for all browser verification, against
   `http://localhost:3000`. Capture before/after screenshots.
3. **Honor the design system:** slate/gray palette, status colors green/blue/yellow/red, custom SVG
   charts, CSS Grid layouts, and **no emojis in the UI**.
4. **Preserve all existing behavior:** routing, the 4-filter `FilterBar`, i18n (`en`/`ja`), the profile
   menu, the language switcher, and the tasks/profile modals must all keep working.
5. After significant changes, run the **`code-reviewer`** subagent.

---

## This app's current UI (ground truth)

Confirm these still hold before you start (read the files — don't assume):

- **Layout owner:** `client/src/App.vue`. Its `<style>` block is **global (not scoped)** and holds the
  whole design system (`.top-nav`, `.card`, `.stat-card`, `.badge`, tables, etc.).
- **Current shell:** `.app` is `display:flex; flex-direction:column` →
  `.top-nav` (sticky, `height:70px`) containing `.nav-container` ( `.logo` + `.nav-tabs` router-links +
  `<LanguageSwitcher />` + `<ProfileMenu />` ) → `<FilterBar />` → `.main-content`
  (`max-width:1600px`, centered, `padding:1.5rem 2rem`) → `<router-view />` → modals.
- **Routes & nav items** (defined in `client/src/main.js`, rendered in `App.vue`). These six links must
  appear in the new sidebar **in this order**, with the same i18n keys:

  | Path         | View          | Label source            |
  |--------------|---------------|-------------------------|
  | `/`          | Dashboard.vue | `t('nav.overview')`     |
  | `/inventory` | Inventory.vue | `t('nav.inventory')`    |
  | `/orders`    | Orders.vue    | `t('nav.orders')`       |
  | `/spending`  | Spending.vue  | `t('nav.finance')`      |
  | `/demand`    | Demand.vue    | `t('nav.demandForecast')` |
  | `/reports`   | Reports.vue   | hardcoded `"Reports"` ← fix: add `nav.reports` to `en.js`/`ja.js` |

- **Active link styling today:** `.nav-tabs a.active` → blue text `#2563eb`, bg `#eff6ff`, 2px bottom bar.
- **Components living in the nav:** `LanguageSwitcher.vue`, `ProfileMenu.vue` (the latter emits
  `show-profile-details` and `show-tasks`). They must be relocated into the sidebar footer and keep
  emitting the same events.
- **Design tokens are hardcoded hex, scattered** across `App.vue` and every component. Key values to
  consolidate: slates `#0f172a #1e293b #334155 #475569 #64748b #94a3b8 #cbd5e1 #e2e8f0 #f1f5f9 #f8fafc`,
  accent `#2563eb` / `#eff6ff`, status `#059669` (success) `#ea580c` (warning) `#dc2626` (danger)
  `#2563eb` (info). Font: `Inter`. Radii: `6px`/`8px`/`10px`. App bg: `#f8fafc`.

---

## Target design

- **Fixed left sidebar**, `--sidebar-width: 248px`, full height, with: brand block at top, vertical nav
  list in the middle, language switcher + profile menu pinned to the footer.
- **Main column** scrolls independently: a slim sticky topbar (page title + breadcrumb area) is optional;
  the existing `FilterBar` sits directly under it, then the routed view in a content container with a
  consistent max width and a single spacing scale.
- **Collapsible** sidebar (icon-rail at `--sidebar-width-collapsed: 72px`) and **responsive**: below
  1024px the sidebar becomes an off-canvas drawer toggled by a hamburger button in the topbar.
- **One spacing scale** (`--space-1..8`), one radius scale, one shadow scale, applied everywhere so
  cards, gaps, and page padding are uniform.

---

## Workflow

### Step 0 — Baseline
1. Ensure dev servers are up (invoke the **`start`** skill, or `cd client && npm run dev` +
   `cd server && uv run python main.py`). Frontend on `:3000`, backend on `:8001`.
2. With Playwright MCP, screenshot all six routes at **1440px** and **375px** widths. Save as the
   "before" set — you will diff against these at the end.
3. Read `App.vue`, `main.js`, `FilterBar.vue`, `ProfileMenu.vue`, `LanguageSwitcher.vue`,
   `composables/useI18n.js`, and the locale files so your changes integrate with real names.

### Step 1 — Add the design-token layer
Introduce CSS custom properties so the redesign is consistent and future-proof. Add a `:root` block at
the top of `App.vue`'s global `<style>` (this is a `.vue` edit → **vue-expert**). Use the snippet in
*Reference: design tokens* below. Do **not** rip out every hardcoded hex in one pass — define the tokens,
then use them in the new sidebar/shell CSS and the spacing pass.

### Step 2 — Build the sidebar shell
Restructure `App.vue` (→ **vue-expert**) from a column to a **row** layout:
- Change `.app` to `display:flex; flex-direction:row`.
- Add `<aside class="sidebar">` as the first child: brand at top, `<nav class="side-nav">` with the six
  `router-link`s (preserve order + i18n keys from the table above), and a `.sidebar-footer` holding
  `<LanguageSwitcher />` and `<ProfileMenu />` (keep their event wiring intact).
- Wrap the rest in `<div class="main-col">`: optional `<header class="topbar">` (hamburger + page area)
  → `<FilterBar />` → `<main class="main-content">` → `<router-view />`. Keep the modals where they are.
- Use the *Reference: shell markup* and *Reference: sidebar CSS* snippets. Active state via
  `router-link`'s `.router-link-active` / explicit `:class="{ active: $route.path === '...' }"` to match
  current behavior.

Consider extracting the sidebar into its own `client/src/components/Sidebar.vue` (also via **vue-expert**)
if `App.vue` gets unwieldy — props in nav items, emits for the profile/tasks events.

Checkpoint: load `:3000`, confirm the sidebar renders, all six links route correctly, active highlight
works, and the language switcher + profile menu still open their modals.

### Step 3 — Consistent spacing & polish pass
Apply the token scale everywhere it matters (→ **vue-expert** for each `.vue` file touched):
- Replace ad-hoc paddings/margins/gaps in the shell, `.card`, `.stat-card`, `.page-header`, and
  `FilterBar` with `var(--space-*)`.
- Standardize content width (`--content-max: 1280px`), card radius (`--radius-lg`), borders
  (`var(--border)`), and shadows (`var(--shadow-sm/md)`).
- Refine focus-visible states, hover transitions, and the topbar. Add the missing `nav.reports` i18n key
  to `en.js` and `ja.js` and switch the Reports link to `t('nav.reports')`.

### Step 4 — Per-view sanity
For each of the six views, confirm nothing depended on the removed `.top-nav`/`.nav-container`
selectors, the `FilterBar` still drives data, and headers/cards align to the new spacing. Fix view-local
styles via **vue-expert** as needed.

### Step 5 — Verify in the browser
With Playwright MCP:
1. Re-screenshot all six routes at 1440px and 375px ("after" set) and compare to the baseline.
2. Confirm: sidebar nav + active states, collapse toggle, mobile drawer open/close, FilterBar behavior,
   profile/tasks/profile-details modals, language switch (en↔ja).
3. Check the browser console for errors/warnings (no `v-for` key warnings, no 404s beyond the known
   `/api/tasks` + `/api/purchase-orders` gaps).

### Step 6 — Review
Run the **`code-reviewer`** subagent over the diff. Verify: no emojis introduced, tokens used instead of
new hardcoded hex, accessibility (nav landmark, `aria-current` on active link, focus order), and that the
design-system palette is intact.

---

## Reference: design tokens

```css
:root {
  /* Spacing scale (4px base) */
  --space-1: 0.25rem; --space-2: 0.5rem;  --space-3: 0.75rem; --space-4: 1rem;
  --space-5: 1.25rem; --space-6: 1.5rem;  --space-8: 2rem;    --space-10: 2.5rem;

  /* Surfaces & lines */
  --bg: #f8fafc; --surface: #ffffff;
  --border: #e2e8f0; --border-strong: #cbd5e1; --divider: #f1f5f9;

  /* Text (slate ramp) */
  --text-strong: #0f172a; --text: #1e293b; --text-muted: #64748b; --text-faint: #94a3b8;

  /* Accent + states */
  --accent: #2563eb; --accent-soft: #eff6ff;
  --success: #059669; --warning: #ea580c; --danger: #dc2626; --info: #2563eb;

  /* Radius / shadow / typography */
  --radius-sm: 6px; --radius-md: 8px; --radius-lg: 10px;
  --shadow-sm: 0 1px 3px rgba(15,23,42,0.05);
  --shadow-md: 0 4px 12px rgba(15,23,42,0.08);
  --font: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;

  /* Layout */
  --sidebar-width: 248px; --sidebar-width-collapsed: 72px; --content-max: 1280px;
}
```

## Reference: shell markup (App.vue template)

```vue
<div class="app" :class="{ 'sidebar-collapsed': collapsed }">
  <aside class="sidebar">
    <div class="brand">
      <h1>{{ t('nav.companyName') }}</h1>
      <span class="brand-sub">{{ t('nav.subtitle') }}</span>
    </div>
    <nav class="side-nav" aria-label="Primary">
      <router-link to="/" :class="{ active: $route.path === '/' }">{{ t('nav.overview') }}</router-link>
      <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }">{{ t('nav.inventory') }}</router-link>
      <router-link to="/orders" :class="{ active: $route.path === '/orders' }">{{ t('nav.orders') }}</router-link>
      <router-link to="/spending" :class="{ active: $route.path === '/spending' }">{{ t('nav.finance') }}</router-link>
      <router-link to="/demand" :class="{ active: $route.path === '/demand' }">{{ t('nav.demandForecast') }}</router-link>
      <router-link to="/reports" :class="{ active: $route.path === '/reports' }">{{ t('nav.reports') }}</router-link>
    </nav>
    <div class="sidebar-footer">
      <LanguageSwitcher />
      <ProfileMenu @show-profile-details="showProfileDetails = true" @show-tasks="showTasks = true" />
    </div>
  </aside>

  <div class="main-col">
    <header class="topbar">
      <button class="nav-toggle" @click="collapsed = !collapsed" aria-label="Toggle navigation">≡</button>
    </header>
    <FilterBar />
    <main class="main-content">
      <router-view />
    </main>
  </div>

  <!-- keep the existing modals here -->
</div>
```
Note: `collapsed` is a new `ref(false)` added to `setup()`. The hamburger glyph above is plain text, not
an emoji; prefer an inline SVG icon to stay consistent with the design system.

## Reference: sidebar CSS (App.vue global style)

```css
.app { display: flex; flex-direction: row; min-height: 100vh; background: var(--bg); }

.sidebar {
  width: var(--sidebar-width); flex-shrink: 0;
  display: flex; flex-direction: column;
  background: var(--surface); border-right: 1px solid var(--border);
  position: sticky; top: 0; height: 100vh;
}
.brand { padding: var(--space-6) var(--space-5); border-bottom: 1px solid var(--divider); }
.brand h1 { font-size: 1.125rem; font-weight: 700; color: var(--text-strong); letter-spacing: -0.02em; }
.brand-sub { font-size: 0.75rem; color: var(--text-muted); }

.side-nav { display: flex; flex-direction: column; gap: var(--space-1); padding: var(--space-4) var(--space-3); flex: 1; }
.side-nav a {
  display: flex; align-items: center; gap: var(--space-3);
  padding: var(--space-3) var(--space-4); border-radius: var(--radius-md);
  color: var(--text-muted); text-decoration: none; font-weight: 500; font-size: 0.938rem;
  transition: background .15s ease, color .15s ease;
}
.side-nav a:hover { color: var(--text-strong); background: var(--divider); }
.side-nav a.active { color: var(--accent); background: var(--accent-soft); }

.sidebar-footer { padding: var(--space-4) var(--space-3); border-top: 1px solid var(--divider);
  display: flex; flex-direction: column; gap: var(--space-3); }

.main-col { flex: 1; min-width: 0; display: flex; flex-direction: column; }
.topbar { height: 60px; display: flex; align-items: center; padding: 0 var(--space-6);
  background: var(--surface); border-bottom: 1px solid var(--border); position: sticky; top: 0; z-index: 50; }
.nav-toggle { display: none; background: none; border: 0; font-size: 1.25rem; cursor: pointer; color: var(--text); }
.main-content { flex: 1; width: 100%; max-width: var(--content-max); margin: 0 auto; padding: var(--space-6) var(--space-8); }

/* Collapsed icon-rail */
.sidebar-collapsed .sidebar { width: var(--sidebar-width-collapsed); }
.sidebar-collapsed .brand-sub,
.sidebar-collapsed .side-nav a span,
.sidebar-collapsed .brand h1 { display: none; }

/* Responsive: off-canvas drawer */
@media (max-width: 1024px) {
  .nav-toggle { display: block; }
  .sidebar { position: fixed; left: 0; top: 0; z-index: 200; transform: translateX(-100%); transition: transform .2s ease; }
  .app:not(.sidebar-collapsed) .sidebar { transform: translateX(0); box-shadow: var(--shadow-md); }
}
```

---

## Verification checklist

- [ ] Top nav fully removed; left sidebar present on every route.
- [ ] All six links route correctly and show the active state (`aria-current` set).
- [ ] Language switcher and profile menu work from the sidebar footer; all modals still open.
- [ ] FilterBar still filters data across views.
- [ ] Collapse toggle and mobile drawer both work; no horizontal scroll at 375px.
- [ ] `nav.reports` key added to `en.js` + `ja.js`; Reports link no longer hardcoded.
- [ ] Spacing/cards/radii use tokens; no new hardcoded hex; palette unchanged.
- [ ] No emojis; no new console errors or `v-for` key warnings.
- [ ] Before/after Playwright screenshots captured at 1440px and 375px.
- [ ] `code-reviewer` run on the final diff.

## Common pitfalls (this app)

- Editing `.vue` files directly instead of delegating to **vue-expert** (project rule).
- Forgetting `App.vue`'s `<style>` is **global** — sidebar selectors leak app-wide; name them clearly.
- Dropping the `ProfileMenu` event wiring (`@show-profile-details`, `@show-tasks`) when relocating it.
- Hardcoding the Reports label — add the i18n key instead.
- Leaving the old `.top-nav` / `.nav-container` rules behind as dead CSS after the switch.
- Introducing emojis as nav "icons" — use inline SVG to honor the design system.
