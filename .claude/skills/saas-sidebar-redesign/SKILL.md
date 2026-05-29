---
name: saas-sidebar-redesign
description: Redesigns the Vue 3 frontend into a modern SaaS-style interface with a left sidebar, tokenized design system, and consistent page spacing. Use this skill when the user asks to redesign the UI, modernize the layout, replace the top nav with a sidebar, or apply a polished SaaS look to the inventory app.
---

# SaaS Sidebar Redesign

This skill performs a deterministic, repeatable redesign of the Factory Inventory Management Vue 3 frontend. It replaces the existing horizontal top-nav layout with a vertical left sidebar and applies a consistent design system across all views.

The redesign must be performed by delegating every `.vue` file modification to the `vue-expert` subagent, per the rule in this repo's root `CLAUDE.md`.

## Goal

- Vertical left sidebar (240px) replacing the current top nav.
- Tokenized design system: colors, spacing scale, typography, radius, shadow.
- A consistent page-header pattern adopted by every view.
- Polished, modern, professional SaaS appearance.

## Non-goals

- Do **not** change routes in `client/src/main.js`.
- Do **not** touch backend code, `client/src/api.js`, or JSON data.
- Do **not** remove or rename existing components — only restyle and relocate.
- Do **not** add new dependencies (no Tailwind, icon library, or UI kit). Icons are inline SVG.
- Do **not** break i18n. Keep all existing `t('nav.*')` keys.

## Files this skill modifies

| File | Change |
|------|--------|
| `client/src/App.vue` | Replace template root with `[sidebar | main-column]`. Replace `:root` tokens and nav styles. |
| `client/src/components/FilterBar.vue` | Restyle to fit inside a sticky page subheader in the main column. |
| `client/src/components/ProfileMenu.vue` | Restyle to sit in the sidebar footer (full-width chip pattern). |
| `client/src/components/LanguageSwitcher.vue` | Restyle to sit next to ProfileMenu in the sidebar footer. |
| `client/src/views/Dashboard.vue` | Wrap content in the canonical `.page` / `.page-header` pattern. |
| `client/src/views/Inventory.vue` | Same. |
| `client/src/views/Orders.vue` | Same. |
| `client/src/views/Demand.vue` | Same. |
| `client/src/views/Spending.vue` | Same. |
| `client/src/views/Reports.vue` | Same. |

Do not modify any other files.

## Design tokens

Place these in the **non-scoped** `<style>` block of `App.vue` (add a second `<style>` element if needed — keep `scoped` for component styles):

```css
:root {
  /* Surfaces */
  --bg: #f8fafc;
  --surface: #ffffff;
  --sidebar: #0f172a;
  --sidebar-ink: #e2e8f0;
  --sidebar-muted: #94a3b8;
  --sidebar-hover: #1e293b;
  --sidebar-active: #1d4ed8;

  /* Text */
  --ink: #0f172a;
  --muted: #64748b;
  --line: #e2e8f0;

  /* Accents */
  --accent: #3b82f6;
  --accent-soft: #eff6ff;
  --success: #10b981;
  --warning: #f59e0b;
  --danger: #ef4444;

  /* Spacing scale (8px base) */
  --space-1: 4px;
  --space-2: 8px;
  --space-3: 12px;
  --space-4: 16px;
  --space-5: 24px;
  --space-6: 32px;
  --space-7: 48px;

  /* Radius */
  --radius-sm: 6px;
  --radius-md: 10px;
  --radius-lg: 14px;

  /* Shadow */
  --shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.04);
  --shadow-md: 0 4px 12px rgba(15, 23, 42, 0.06);

  /* Layout */
  --sidebar-width: 240px;
  --sidebar-collapsed: 64px;
  --subheader-height: 64px;

  /* Typography */
  --font-stack: -apple-system, BlinkMacSystemFont, "Segoe UI", system-ui, sans-serif;
}

body {
  margin: 0;
  background: var(--bg);
  color: var(--ink);
  font-family: var(--font-stack);
  font-size: 0.92rem;
  line-height: 1.55;
  -webkit-font-smoothing: antialiased;
}
```

## App.vue — new template

Replace the existing `<template>` block with this structure. Keep the existing `<script>` block unchanged (it stays — same imports, same setup, same modals).

```vue
<template>
  <div class="app">
    <aside class="sidebar">
      <div class="sidebar-brand">
        <div class="brand-mark">FI</div>
        <div class="brand-text">
          <div class="brand-title">{{ t('nav.companyName') }}</div>
          <div class="brand-subtitle">{{ t('nav.subtitle') }}</div>
        </div>
      </div>

      <nav class="sidebar-nav">
        <router-link to="/" :class="{ active: $route.path === '/' }">
          <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2"><rect x="3" y="3" width="7" height="9"/><rect x="14" y="3" width="7" height="5"/><rect x="14" y="12" width="7" height="9"/><rect x="3" y="16" width="7" height="5"/></svg>
          <span>{{ t('nav.overview') }}</span>
        </router-link>
        <router-link to="/inventory" :class="{ active: $route.path === '/inventory' }">
          <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2"><path d="M21 16V8a2 2 0 0 0-1-1.73l-7-4a2 2 0 0 0-2 0l-7 4A2 2 0 0 0 3 8v8a2 2 0 0 0 1 1.73l7 4a2 2 0 0 0 2 0l7-4A2 2 0 0 0 21 16z"/><polyline points="3.27 6.96 12 12.01 20.73 6.96"/><line x1="12" y1="22.08" x2="12" y2="12"/></svg>
          <span>{{ t('nav.inventory') }}</span>
        </router-link>
        <router-link to="/orders" :class="{ active: $route.path === '/orders' }">
          <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2"><path d="M6 2 3 6v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2V6l-3-4z"/><line x1="3" y1="6" x2="21" y2="6"/><path d="M16 10a4 4 0 0 1-8 0"/></svg>
          <span>{{ t('nav.orders') }}</span>
        </router-link>
        <router-link to="/spending" :class="{ active: $route.path === '/spending' }">
          <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2"><line x1="12" y1="1" x2="12" y2="23"/><path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"/></svg>
          <span>{{ t('nav.finance') }}</span>
        </router-link>
        <router-link to="/demand" :class="{ active: $route.path === '/demand' }">
          <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2"><polyline points="23 6 13.5 15.5 8.5 10.5 1 18"/><polyline points="17 6 23 6 23 12"/></svg>
          <span>{{ t('nav.demandForecast') }}</span>
        </router-link>
        <router-link to="/reports" :class="{ active: $route.path === '/reports' }">
          <svg viewBox="0 0 24 24" width="18" height="18" fill="none" stroke="currentColor" stroke-width="2"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/></svg>
          <span>Reports</span>
        </router-link>
      </nav>

      <div class="sidebar-footer">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </div>
    </aside>

    <div class="layout-main">
      <div class="subheader">
        <FilterBar />
      </div>
      <main class="content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />
    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>
```

## App.vue — new styles (replace `.top-nav`, `.nav-container`, `.nav-tabs`, `.main-content`)

```css
.app {
  min-height: 100vh;
  display: flex;
}

/* Sidebar */
.sidebar {
  position: fixed;
  inset: 0 auto 0 0;
  width: var(--sidebar-width);
  background: var(--sidebar);
  color: var(--sidebar-ink);
  display: flex;
  flex-direction: column;
  padding: var(--space-5) var(--space-3);
  z-index: 50;
}

.sidebar-brand {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: 0 var(--space-3) var(--space-5);
  border-bottom: 1px solid var(--sidebar-hover);
  margin-bottom: var(--space-4);
}
.brand-mark {
  width: 36px; height: 36px;
  border-radius: var(--radius-sm);
  background: var(--accent);
  color: white;
  display: flex; align-items: center; justify-content: center;
  font-weight: 700; font-size: 0.85rem;
}
.brand-title { font-weight: 600; font-size: 0.95rem; color: white; }
.brand-subtitle { font-size: 0.75rem; color: var(--sidebar-muted); }

.sidebar-nav { display: flex; flex-direction: column; gap: var(--space-1); flex: 1; }
.sidebar-nav a {
  display: flex; align-items: center; gap: var(--space-3);
  padding: var(--space-3) var(--space-3);
  border-radius: var(--radius-sm);
  color: var(--sidebar-ink);
  text-decoration: none;
  font-size: 0.9rem;
  font-weight: 500;
  transition: background 0.15s, color 0.15s;
}
.sidebar-nav a:hover { background: var(--sidebar-hover); }
.sidebar-nav a.active {
  background: var(--sidebar-active);
  color: white;
}
.sidebar-nav a svg { flex-shrink: 0; opacity: 0.9; }

.sidebar-footer {
  border-top: 1px solid var(--sidebar-hover);
  padding-top: var(--space-3);
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
}

/* Main column */
.layout-main {
  margin-left: var(--sidebar-width);
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
}

.subheader {
  position: sticky;
  top: 0;
  z-index: 10;
  background: var(--surface);
  border-bottom: 1px solid var(--line);
  padding: var(--space-3) var(--space-6);
}

.content {
  padding: var(--space-5) var(--space-6) var(--space-7);
  max-width: 1400px;
  width: 100%;
}

/* Responsive collapse */
@media (max-width: 900px) {
  .sidebar { width: var(--sidebar-collapsed); padding: var(--space-4) var(--space-2); }
  .sidebar-brand .brand-text,
  .sidebar-nav a span { display: none; }
  .sidebar-nav a { justify-content: center; }
  .layout-main { margin-left: var(--sidebar-collapsed); }
  .content { padding: var(--space-4); }
}
```

## Canonical view pattern

Every view in `client/src/views/` must adopt this wrapper. Remove any duplicate page-level `<h1>` already present — the new `.page-header` is the single source of truth for view titles.

```vue
<template>
  <div class="page">
    <header class="page-header">
      <div>
        <h1>{{ t('page.title') }}</h1>
        <p class="page-subtitle">{{ t('page.subtitle') }}</p>
      </div>
      <div class="page-actions">
        <!-- existing primary action buttons go here -->
      </div>
    </header>
    <div class="page-body">
      <!-- preserve all existing content here, unchanged -->
    </div>
  </div>
</template>
```

Scoped styles to add to each view (or hoist into App.vue as a non-scoped block — prefer non-scoped to enforce consistency):

```css
.page { display: flex; flex-direction: column; gap: var(--space-5); }
.page-header {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: var(--space-4);
}
.page-header h1 {
  margin: 0;
  font-size: 1.5rem;
  font-weight: 600;
  letter-spacing: -0.01em;
}
.page-subtitle {
  margin: 4px 0 0;
  color: var(--muted);
  font-size: 0.9rem;
}
.page-actions { display: flex; gap: var(--space-2); }
.page-body { display: flex; flex-direction: column; gap: var(--space-4); }

/* Card primitive used inside .page-body */
.card {
  background: var(--surface);
  border: 1px solid var(--line);
  border-radius: var(--radius-md);
  padding: var(--space-5);
  box-shadow: var(--shadow-sm);
}
.card h2 { margin: 0 0 var(--space-4); font-size: 1.05rem; font-weight: 600; }
```

## FilterBar.vue — restyle

`FilterBar` now lives inside the sticky `.subheader`. Update its scoped styles so it:

- Has no outer background (the subheader provides it)
- Lays out filters with `display: flex; gap: var(--space-3); align-items: center;`
- Uses `select`/`button` styling consistent with the design tokens (border `1px solid var(--line)`, radius `var(--radius-sm)`, padding `6px 10px`, font 0.85rem)
- Removes any prior top/bottom borders or shadows

## ProfileMenu.vue & LanguageSwitcher.vue — restyle

Both must render compactly inside the dark sidebar footer:

- Backgrounds: `var(--sidebar-hover)`; text: `var(--sidebar-ink)`
- Full-width within the footer (`width: 100%`)
- Dropdowns/popovers open **upward** (the footer is at the bottom of the sidebar). Use `bottom: 100%` instead of `top: 100%` on the menu.
- Padding `var(--space-2) var(--space-3)`; radius `var(--radius-sm)`; font 0.85rem

Do not change their props, events, or `<script>` logic — only template structure (if needed for layout) and scoped CSS.

## Execution checklist

When invoked, perform these steps in order. Every `.vue` modification MUST go through the `vue-expert` subagent.

1. Read `client/src/App.vue` to confirm current line numbers for the nav block and styles.
2. Delegate to `vue-expert`: apply the new template + new styles + tokens to `App.vue`. Keep `<script>` intact.
3. Delegate to `vue-expert`: restyle `client/src/components/FilterBar.vue` per the spec above.
4. Delegate to `vue-expert`: restyle `client/src/components/ProfileMenu.vue` and `client/src/components/LanguageSwitcher.vue` for the sidebar footer (dropdown opens upward).
5. Delegate to `vue-expert`: sweep `client/src/views/{Dashboard,Inventory,Orders,Demand,Spending,Reports}.vue` to wrap content in the `.page` / `.page-header` / `.page-body` pattern. Remove any duplicate page titles each view previously rendered.
6. Run dev servers (`/start` skill) and visit http://localhost:3000.
7. Verify in browser: sidebar visible, all 6 routes navigate, FilterBar functional in subheader, modals still open, language switcher works, responsive collapse triggers at ≤900px.

## Guardrails (read before acting)

- Do not modify `client/src/api.js`, `client/src/main.js`, `server/`, `tests/`, or any JSON.
- Do not change route paths or i18n keys.
- Do not add npm dependencies.
- Use the `vue-expert` subagent for every `.vue` edit — this is a hard rule from `CLAUDE.md`.
- Use unique `v-for` keys (sku, id) — never index — when rewriting any list in a view.
- Preserve all existing functionality (FilterBar, ProfileMenu, LanguageSwitcher, modals, i18n).
