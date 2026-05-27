---
name: vue-optimization-analyzer
description: Statically analyze the Vue 3 frontend's component structure and produce a prioritized, read-only report of performance and code-reuse optimization opportunities (reactivity, v-for keys, computed vs methods, duplicated markup/logic, missing composables/utils, prop drilling, code-splitting). Use this skill when asked to analyze Vue components, audit frontend performance, find refactor/reuse opportunities, or review component structure — when the user wants findings and recommendations, not code changes.
---

# Vue Component Optimization Analyzer

This skill performs a **static, read-only audit** of the Factory Inventory Management Vue 3 frontend
(`client/src/`) and produces a **prioritized report** of performance and code-reuse opportunities.

It is **analysis only**: it never edits `.vue` files and never starts servers. The deliverable is a
written report the user can act on. If the user later wants the fixes applied, hand each `.vue` change
to the **`vue-expert`** subagent (project rule) — that is out of scope for this skill.

---

## Critical rules (read before starting)

1. **Read-only.** Do NOT modify any file. No `Edit`/`Write` to source, no auto-fixes, no `git` changes.
   The output is a report only.
2. **Static only.** Do NOT start the dev servers or use Playwright. Reason about source code only.
3. **Use the `Explore` subagent** to map the codebase and locate patterns (project rule in `CLAUDE.md`).
   Reach for `Grep`/`Read` directly for targeted confirmation of a specific finding.
4. **Ground every finding in real evidence.** Cite `file:line`. Never report a generic "best practice"
   that the code doesn't actually violate. If you can't point to the line, drop the finding.
5. **Prioritize ruthlessly.** A long list of trivia is noise. Rank by impact × confidence (see rubric).
6. **Honor this app's context** (`CLAUDE.md`): Composition API + `<script setup>`/`setup()`, raw data in
   refs (`allOrders`, `inventoryItems`) with derived data in `computed`, the 4-filter system, i18n
   (`en`/`ja`), custom SVG charts, and the known-incomplete features (`/api/tasks` 404,
   missing `PurchaseOrderModal`) — do NOT flag those as bugs.

---

## App layout (ground truth — confirm by reading, don't assume)

```
client/src/
├── App.vue                  # shell + GLOBAL (unscoped) <style> design system
├── main.js                  # router + app bootstrap
├── api.js                   # API client
├── components/              # FilterBar, ProfileMenu, LanguageSwitcher, *DetailModal (x5), TasksModal, ...
├── views/                   # Dashboard, Inventory, Orders, Spending, Demand, Reports, Backlog
├── composables/             # shared reactive logic (the right home for duplicated logic)
├── utils/                   # pure helpers (the right home for duplicated formatting)
└── locales/                 # en / ja i18n strings
```

Note the cluster of `*DetailModal.vue` components (Backlog/Cost/Inventory/Product detail + Profile/Tasks) —
this family is the prime suspect for **duplicated markup/logic** that wants a shared base component.

---

## Workflow

### Step 0 — Map the surface
Use the **`Explore`** subagent to build an inventory before analyzing:
- Every `.vue` file with its line count (size = complexity signal):
  `find client/src -name '*.vue' | xargs wc -l | sort -rn`
- The composables and utils that already exist (so you recommend *adding to* them, not reinventing):
  `find client/src/composables client/src/utils -type f`
- The router config in `main.js` (for code-splitting opportunities).

Produce a short component map: which views use which components, and which components repeat.

### Step 1 — Performance pass
Walk the *Performance check catalog* below. For each component, look for the listed anti-patterns and
record concrete hits with `file:line`. Confirm suspected hits by reading the surrounding code — grep
finds candidates, reading confirms them.

### Step 2 — Code-reuse pass
Walk the *Code-reuse check catalog*. Focus on duplication across the modal family and across views:
repeated formatting, repeated fetch/filter logic, repeated markup blocks, repeated constants. Each
finding should name the **target abstraction** (a specific composable in `composables/`, a helper in
`utils/`, a base component, a slot, or a prop) — not just "this is duplicated."

### Step 3 — Structure pass
Walk the *Structure check catalog*: oversized "god" components, mixed concerns, prop drilling that wants
`provide/inject`, inconsistent component APIs (props/emits naming), and global-style leakage from
`App.vue`.

### Step 4 — Prioritize & score
Assign each finding a **severity** (rubric below) = impact × confidence, and an **effort** estimate
(S/M/L). Drop anything you can't evidence. De-duplicate overlapping findings.

### Step 5 — Write the report
Emit the report using the *Report template* below. Lead with a one-paragraph summary and the top 3
highest-leverage changes, then the full table grouped by category, then per-finding detail. Do not edit
any files. End by offering: "Want me to apply any of these? I'll route each `.vue` change through
`vue-expert`."

---

## Performance check catalog (Vue 3)

For each: **what to look for**, a **detection hint**, and the **fix**.

1. **`v-for` with index as key (or missing key).**
   - Hint: `grep -rn 'v-for' client/src --include='*.vue'` then inspect each `:key`.
   - Why: index keys break list-diffing on reorder/insert → wrong DOM reuse, subtle bugs, wasted patches.
   - Fix: key on a stable unique field (`sku`, `id`, `order_number`, `month`). (Called out in `CLAUDE.md`.)

2. **Derived state computed in methods / inline in template instead of `computed`.**
   - Hint: template calls like `{{ formatThing(x) }}` or `{{ items.filter(...) }}`; functions in `methods`
     / `setup` returning derived values that don't take per-call args.
   - Why: methods re-run on every render with no caching; `computed` caches until deps change.
   - Fix: convert to a `computed`. Keep `methods` only for event handlers / parameterized calls.

3. **`v-for` + `v-if` on the same element.**
   - Hint: `grep -rn 'v-for' client/src --include='*.vue'` and check for a sibling `v-if`.
   - Why: filtering inside the loop runs every render. Fix: filter via a `computed`, or wrap in `<template v-if>`.

4. **New object/array/function literals in the template.**
   - Hint: `:style="{ ... }"`, `:class="[ ... ]"`, `@click="() => ..."`, `:prop="{ ... }"` inline.
   - Why: a fresh reference every render defeats child memoization and can churn watchers.
   - Fix: hoist to a `computed` or a named handler.

5. **Over-deep reactivity on large/static data.**
   - Hint: big JSON datasets or chart inputs stored in `ref`/`reactive`; deep `watch`.
   - Why: deep reactivity proxies every nested property. Static lookup tables don't need it.
   - Fix: `shallowRef` / `markRaw` for large immutable data; scope watchers narrowly.

6. **`v-if` where `v-show` fits (or vice-versa).**
   - Why: `v-if` mounts/unmounts (costly for frequently toggled UI like modals/tabs); `v-show` toggles CSS.
   - Fix: `v-show` for frequent toggles of cheap subtrees; keep `v-if` for rarely-shown/expensive ones.

7. **No route-level code splitting.**
   - Hint: in `main.js`, `import Dashboard from './views/...'` (static) vs `() => import('./views/...')`.
   - Why: all views ship in one bundle. Lazy routes cut initial load.
   - Fix: dynamic `import()` per route; `defineAsyncComponent` for heavy modals.

8. **Static / expensive subtrees re-rendering.**
   - Hint: large constant markup (SVG chart scaffolding, headers) inside frequently-updating components.
   - Fix: `v-once` for truly static subtrees; `v-memo` for expensive lists keyed on stable deps.

9. **Listeners/timers without cleanup.**
   - Hint: `addEventListener`, `setInterval`, `setTimeout`, observers in `onMounted` with no matching
     `onUnmounted`/`clearInterval`.
   - Why: leaks and ghost handlers after unmount. Fix: clean up in `onUnmounted`.

10. **Unstable / heavy watchers.**
    - Hint: `watch(..., { deep: true })`, watchers that just recompute a value, watchers triggering fetches
      without debounce.
    - Fix: prefer `computed`; narrow the watched source; debounce filter-driven fetches.

11. **Broad library imports.**
    - Hint: `import _ from 'lodash'`, full-icon-set imports, etc.
    - Fix: import only what's used (`import debounce from 'lodash/debounce'`) for tree-shaking.

---

## Code-reuse check catalog

1. **Duplicated formatting (currency / dates / numbers).**
   - Hint: `grep -rn 'toLocaleString\|toFixed\|Intl\.\|new Date(' client/src --include='*.vue'`.
   - Fix: centralize in `client/src/utils/` (e.g. `formatCurrency`, `formatDate`) and import everywhere.
     Also fixes the `CLAUDE.md` "validate dates before `.getMonth()`" footgun in one place.

2. **Duplicated modal markup/logic (the `*DetailModal` family).**
   - Hint: compare `BacklogDetailModal`, `CostDetailModal`, `InventoryDetailModal`, `ProductDetailModal`,
     `ProfileDetailsModal`, `TasksModal` for shared overlay/header/close/escape-key structure.
   - Fix: extract a `BaseModal.vue` (slots for header/body/footer, shared `@close`/escape/overlay), or a
     `useModal()` composable for open/close/escape wiring. Big win — this is the densest duplication.

3. **Repeated data-fetching / filter-param logic.**
   - Hint: each view assembling the same `warehouse/category/status/month` query params and calling `api.js`.
   - Fix: a `useFilteredResource(endpoint)` composable (or shared helper in `api.js`) that owns
     loading/error/refetch and the filter-to-query mapping.

4. **Repeated reactive patterns across views.**
   - Hint: identical `ref` + `computed` + fetch-on-mount scaffolding duplicated per view.
   - Fix: a composable encapsulating the load/derive lifecycle.

5. **Magic strings/numbers duplicated.**
   - Hint: warehouse names, category lists, status enums, revenue goals ($800K/mo, $9.6M YTD),
     status→color maps repeated inline.
   - Fix: a shared `constants.js` in `utils/` (and use i18n keys for display labels).

6. **Copy-pasted markup that wants slots/props.**
   - Hint: near-identical card/table/badge blocks differing only by data.
   - Fix: a small presentational component (`StatCard`, `DataTable`, `StatusBadge`) parameterized by props/slots.

7. **Inconsistent currency/i18n conversion (reuse gap with a correctness consequence).**
   - Hint: some files import the shared formatter (`utils/currency.js` → `formatCurrency`) while others
     hand-roll a local `currencySymbol` computed and prefix it to a raw `amount.toLocaleString()`/`toFixed()`.
     `grep -rn 'currencySymbol' client/src --include='*.vue'` and compare against who imports `utils/currency`.
   - Why: bypassing the shared helper doesn't just duplicate code — it **skips the conversion logic**
     (e.g. USD→JPY), so the same value renders differently (or wrongly) across screens depending on which
     path it took. This is the most valuable class of reuse finding because it carries a real defect, not
     just maintenance cost.
   - Fix: route every monetary/locale-sensitive render through the shared helper
     (`formatCurrency` / `formatCurrencyWithDecimals`), and delete the per-component `currencySymbol` duplicates.
     Flag the correctness angle explicitly in the report, not just the duplication.

---

## Structure check catalog

1. **God components.** Largest `.vue` files (from Step 0). Flag views mixing fetching + heavy derivation +
   large templates; suggest splitting presentational children out.
2. **Prop drilling.** Props threaded ≥2 levels unchanged → suggest `provide/inject` (e.g. shared filter
   state) or a composable.
3. **Inconsistent component API.** Mixed prop/emit naming, missing `emits` declarations, missing prop
   types/defaults across the modal family → suggest a consistent contract.
4. **Global style leakage.** `App.vue`'s `<style>` is **unscoped** (whole design system). Flag generic
   selectors that can leak app-wide; suggest scoping component-specific rules or namespacing.

---

## Severity rubric (impact × confidence)

- **High** — measurable perf/correctness impact OR removes substantial duplication, and the evidence is
  unambiguous. (e.g. index keys on a large dynamic list; the whole modal family duplicating overlay logic.)
- **Medium** — real improvement, moderate scope, or slightly lower confidence. (e.g. method→computed on a
  hot template; extracting a formatter.)
- **Low** — minor polish / consistency. (e.g. `v-show` vs `v-if` on a rarely-toggled element.)

Effort: **S** (single file, mechanical) · **M** (new composable/util + a few call sites) · **L** (cross-cutting
refactor like `BaseModal`). Tag each finding `[Severity · Effort]`.

---

## Report template

```markdown
# Vue Optimization Analysis — <date>

## Summary
<2-4 sentences: overall health, where the leverage is.>

## Top 3 highest-leverage changes
1. <finding> — <one-line why> [High · M]
2. ...
3. ...

## Findings by category

### Performance
| # | Finding | Location | Severity · Effort |
|---|---------|----------|-------------------|
| P1 | v-for keyed on index | components/Foo.vue:42 | High · S |
| ... |

### Code reuse
| # | Finding | Location | Target abstraction | Severity · Effort |
|---|---------|----------|--------------------|-------------------|
| R1 | Modal overlay/close duplicated x6 | components/*DetailModal.vue | BaseModal.vue | High · L |
| ... |

### Structure
| ... |

## Detail
For each finding (Pn / Rn / Sn):
- **What:** the issue, with the offending snippet (file:line).
- **Why it matters:** concrete impact (re-renders, bundle size, maintenance cost).
- **Recommended fix:** specific change + where it lives (which composable/util/component).
- **Effort & risk:** S/M/L and anything that could break (routing, i18n, FilterBar wiring).

## Suggested sequence
<Order the fixes — quick wins first, then the high-leverage refactors. Note dependencies.>
```

---

## Common pitfalls (for the analyzer itself)

- **Reporting non-issues.** Don't flag the known-incomplete features (`/api/tasks` 404, missing
  `PurchaseOrderModal`) or call documented design choices "bugs."
- **Generic advice with no `file:line`.** Every finding must point at real code.
- **Editing instead of reporting.** This skill is read-only. Applying fixes = `vue-expert`, separately.
- **Starting servers / Playwright.** Out of scope — static analysis only.
- **Overvaluing micro-optimizations.** A `v-memo` on a 3-row list is noise; the modal-family refactor is signal.
- **Ignoring i18n/FilterBar coupling.** When suggesting extractions, preserve i18n keys and the 4-filter
  reactive flow; call out anything that touches them.
```
