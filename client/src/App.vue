<template>
  <div :class="['app', { 'mobile-nav-open': mobileNavOpen }]">

    <!-- Mobile overlay backdrop -->
    <div class="sidebar-overlay" @click="mobileNavOpen = false"></div>

    <!-- ─── LEFT SIDEBAR ─── -->
    <aside class="sidebar">
      <div class="brand">
        <h1>{{ t('nav.companyName') }}</h1>
        <small class="brand-subtitle">{{ t('nav.subtitle') }}</small>
      </div>

      <nav class="side-nav" aria-label="Primary">
        <!-- Overview -->
        <router-link
          to="/"
          :class="{ active: $route.path === '/' }"
          :aria-current="$route.path === '/' ? 'page' : undefined"
          @click="mobileNavOpen = false"
        >
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M3.75 6A2.25 2.25 0 016 3.75h2.25A2.25 2.25 0 0110.5 6v2.25a2.25 2.25 0 01-2.25 2.25H6a2.25 2.25 0 01-2.25-2.25V6zM3.75 15.75A2.25 2.25 0 016 13.5h2.25a2.25 2.25 0 012.25 2.25V18a2.25 2.25 0 01-2.25 2.25H6A2.25 2.25 0 013.75 18v-2.25zM13.5 6a2.25 2.25 0 012.25-2.25H18A2.25 2.25 0 0120.25 6v2.25A2.25 2.25 0 0118 10.5h-2.25a2.25 2.25 0 01-2.25-2.25V6zM13.5 15.75a2.25 2.25 0 012.25-2.25H18a2.25 2.25 0 012.25 2.25V18A2.25 2.25 0 0118 20.25h-2.25A2.25 2.25 0 0113.5 18v-2.25z"/>
          </svg>
          <span>{{ t('nav.overview') }}</span>
        </router-link>

        <!-- Inventory -->
        <router-link
          to="/inventory"
          :class="{ active: $route.path === '/inventory' }"
          :aria-current="$route.path === '/inventory' ? 'page' : undefined"
          @click="mobileNavOpen = false"
        >
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M21 7.5l-9-5.25L3 7.5m18 0l-9 5.25m9-5.25v9l-9 5.25M3 7.5l9 5.25M3 7.5v9l9 5.25m0-9v9"/>
          </svg>
          <span>{{ t('nav.inventory') }}</span>
        </router-link>

        <!-- Orders -->
        <router-link
          to="/orders"
          :class="{ active: $route.path === '/orders' }"
          :aria-current="$route.path === '/orders' ? 'page' : undefined"
          @click="mobileNavOpen = false"
        >
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M9 12h3.75M9 15h3.75M9 18h3.75m3 .75H18a2.25 2.25 0 002.25-2.25V6.108c0-1.135-.845-2.098-1.976-2.192a48.424 48.424 0 00-1.123-.08m-5.801 0c-.065.21-.1.433-.1.664 0 .414.336.75.75.75h4.5a.75.75 0 00.75-.75 2.25 2.25 0 00-.1-.664m-5.8 0A2.251 2.251 0 0113.5 2.25H15c1.012 0 1.867.668 2.15 1.586m-5.8 0c-.376.023-.75.05-1.124.08C9.095 4.01 8.25 4.973 8.25 6.108V8.25m0 0H4.875c-.621 0-1.125.504-1.125 1.125v11.25c0 .621.504 1.125 1.125 1.125h9.75c.621 0 1.125-.504 1.125-1.125V9.375c0-.621-.504-1.125-1.125-1.125H8.25zM6.75 12h.008v.008H6.75V12zm0 3h.008v.008H6.75V15zm0 3h.008v.008H6.75V18z"/>
          </svg>
          <span>{{ t('nav.orders') }}</span>
        </router-link>

        <!-- Finance -->
        <router-link
          to="/spending"
          :class="{ active: $route.path === '/spending' }"
          :aria-current="$route.path === '/spending' ? 'page' : undefined"
          @click="mobileNavOpen = false"
        >
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M2.25 18.75a60.07 60.07 0 0115.797 2.101c.727.198 1.453-.342 1.453-1.096V18.75M3.75 4.5v.75A.75.75 0 013 6h-.75m0 0v-.375c0-.621.504-1.125 1.125-1.125H20.25M2.25 6v9m18-10.5v.75c0 .414.336.75.75.75h.75m-1.5-1.5h.375c.621 0 1.125.504 1.125 1.125v9.75c0 .621-.504 1.125-1.125 1.125h-.375m1.5-1.5H21a.75.75 0 00-.75.75v.75m0 0H3.75m0 0h-.375a1.125 1.125 0 01-1.125-1.125V15m1.5 1.5v-.75A.75.75 0 003 15h-.75M15 10.5a3 3 0 11-6 0 3 3 0 016 0zm3 0h.008v.008H18V10.5zm-12 0h.008v.008H6V10.5z"/>
          </svg>
          <span>{{ t('nav.finance') }}</span>
        </router-link>

        <!-- Demand Forecast -->
        <router-link
          to="/demand"
          :class="{ active: $route.path === '/demand' }"
          :aria-current="$route.path === '/demand' ? 'page' : undefined"
          @click="mobileNavOpen = false"
        >
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M2.25 18L9 11.25l4.306 4.307a11.95 11.95 0 015.814-5.519l2.74-1.22m0 0l-5.94-2.28m5.94 2.28l-2.28 5.941"/>
          </svg>
          <span>{{ t('nav.demandForecast') }}</span>
        </router-link>

        <!-- Reports -->
        <router-link
          to="/reports"
          :class="{ active: $route.path === '/reports' }"
          :aria-current="$route.path === '/reports' ? 'page' : undefined"
          @click="mobileNavOpen = false"
        >
          <svg width="18" height="18" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
            <path d="M19.5 14.25v-2.625a3.375 3.375 0 00-3.375-3.375h-1.5A1.125 1.125 0 0113.5 7.125v-1.5a3.375 3.375 0 00-3.375-3.375H8.25M9 16.5v.75m3-3v3M15 12v5.25m-4.5-15H5.625c-.621 0-1.125.504-1.125 1.125v17.25c0 .621.504 1.125 1.125 1.125h12.75c.621 0 1.125-.504 1.125-1.125V11.25a9 9 0 00-9-9z"/>
          </svg>
          <span>{{ t('nav.reports') }}</span>
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

    <!-- ─── MAIN COLUMN ─── -->
    <div class="main-col">
      <!-- Mobile topbar with hamburger -->
      <header class="topbar">
        <button
          class="nav-toggle"
          @click="mobileNavOpen = !mobileNavOpen"
          aria-label="Toggle navigation"
          :aria-expanded="mobileNavOpen ? 'true' : 'false'"
        >
          <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" aria-hidden="true">
            <path d="M3.75 6.75h16.5M3.75 12h16.5m-16.5 5.25h16.5"/>
          </svg>
        </button>
      </header>

      <FilterBar />

      <main class="main-content">
        <router-view />
      </main>
    </div>

    <!-- ─── MODALS ─── -->
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

<script>
import { ref, onMounted, computed } from 'vue'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const mobileNavOpen = ref(false)
    const apiTasks = ref([])

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)
        if (isMockTask) {
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)
        if (mockTask) {
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    onMounted(loadTasks)

    return {
      t,
      showProfileDetails,
      showTasks,
      mobileNavOpen,
      tasks,
      addTask,
      deleteTask,
      toggleTask
    }
  }
}
</script>

<style>
/* ─────────────────────────────────────────────────────────────────────────────
   DESIGN TOKENS
───────────────────────────────────────────────────────────────────────────── */
:root {
  --space-1: .25rem;  --space-2: .5rem;   --space-3: .75rem;
  --space-4: 1rem;    --space-5: 1.25rem; --space-6: 1.5rem;
  --space-8: 2rem;    --space-10: 2.5rem;

  --bg: #f8fafc;
  --surface: #ffffff;
  --border: #e2e8f0;
  --border-strong: #cbd5e1;
  --divider: #f1f5f9;

  --text-strong: #0f172a;
  --text: #1e293b;
  --text-muted: #64748b;
  --text-secondary: #334155;
  --text-faint: #94a3b8;

  --accent: #2563eb;
  --accent-soft: #eff6ff;

  --success: #059669;
  --warning: #ea580c;
  --danger: #dc2626;
  --info: #2563eb;

  --radius-sm: 6px;
  --radius-md: 8px;
  --radius-lg: 12px;

  --shadow-sm: 0 1px 2px rgba(15,23,42,.04), 0 1px 3px rgba(15,23,42,.06);
  --shadow-md: 0 4px 12px rgba(15,23,42,.08);

  --sidebar-width: 248px;
  --content-max: 1320px;
  --topbar-height: 52px;
}

/* ─────────────────────────────────────────────────────────────────────────────
   RESET
───────────────────────────────────────────────────────────────────────────── */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: var(--bg);
  color: var(--text);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* ─────────────────────────────────────────────────────────────────────────────
   APP SHELL
───────────────────────────────────────────────────────────────────────────── */
.app {
  display: flex;
  flex-direction: row;
  min-height: 100vh;
}

/* ─────────────────────────────────────────────────────────────────────────────
   SIDEBAR
───────────────────────────────────────────────────────────────────────────── */
.sidebar {
  width: var(--sidebar-width);
  flex-shrink: 0;
  height: 100vh;
  position: sticky;
  top: 0;
  display: flex;
  flex-direction: column;
  background: var(--surface);
  border-right: 1px solid var(--border);
  z-index: 100;
  /* overflow visible so footer dropdowns are not clipped */
  overflow: visible;
}

.brand {
  padding: var(--space-5) var(--space-5) var(--space-4);
  border-bottom: 1px solid var(--divider);
  flex-shrink: 0;
}

.brand h1 {
  font-size: 1rem;
  font-weight: 700;
  color: var(--text-strong);
  letter-spacing: -0.02em;
  line-height: 1.3;
  margin-bottom: var(--space-1);
}

.brand-subtitle {
  font-size: 0.6875rem;
  color: var(--text-faint);
  font-weight: 400;
  letter-spacing: 0.01em;
}

.side-nav {
  flex: 1;
  padding: var(--space-3) var(--space-3);
  display: flex;
  flex-direction: column;
  gap: 2px;
  overflow-y: auto;
}

.side-nav a {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: 0.5625rem var(--space-3);
  color: var(--text-muted);
  text-decoration: none;
  font-size: 0.875rem;
  font-weight: 500;
  border-radius: var(--radius-md);
  transition: color 0.15s ease, background 0.15s ease;
  position: relative;
  white-space: nowrap;
}

.side-nav a:hover {
  color: var(--text-strong);
  background: var(--divider);
}

.side-nav a.active {
  color: var(--accent);
  background: var(--accent-soft);
}

.side-nav a:focus-visible {
  outline: 2px solid var(--accent);
  outline-offset: 2px;
}

.side-nav a.active::before {
  content: '';
  position: absolute;
  left: 0;
  top: 25%;
  bottom: 25%;
  width: 3px;
  background: var(--accent);
  border-radius: 0 2px 2px 0;
}

.side-nav a svg {
  flex-shrink: 0;
}

/* Sidebar footer — no overflow restriction so dropdowns render above viewport edge */
.sidebar-footer {
  padding: var(--space-3) var(--space-3);
  border-top: 1px solid var(--divider);
  display: flex;
  flex-direction: column;
  gap: var(--space-2);
  flex-shrink: 0;
}

/* Open sidebar-footer dropdowns upward so they don't go off-screen */
.sidebar .sidebar-footer .dropdown-menu {
  bottom: calc(100% + 0.375rem);
  top: auto;
}

/* ─────────────────────────────────────────────────────────────────────────────
   MAIN COLUMN
───────────────────────────────────────────────────────────────────────────── */
.main-col {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
}

/* ─────────────────────────────────────────────────────────────────────────────
   TOPBAR  (hamburger strip — visible only on mobile, hidden on desktop)
───────────────────────────────────────────────────────────────────────────── */
.topbar {
  display: none;
  align-items: center;
  height: var(--topbar-height);
  padding: 0 var(--space-4);
  background: var(--surface);
  border-bottom: 1px solid var(--border);
  position: sticky;
  top: 0;
  z-index: 90;
  flex-shrink: 0;
}

.nav-toggle {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 36px;
  height: 36px;
  background: none;
  border: 1px solid var(--border);
  border-radius: var(--radius-sm);
  color: var(--text-muted);
  cursor: pointer;
  transition: background 0.15s ease, color 0.15s ease, border-color 0.15s ease;
}

.nav-toggle:hover {
  background: var(--divider);
  border-color: var(--border-strong);
  color: var(--text-strong);
}

/* ─────────────────────────────────────────────────────────────────────────────
   MAIN CONTENT AREA
───────────────────────────────────────────────────────────────────────────── */
.main-content {
  flex: 1;
  max-width: var(--content-max);
  width: 100%;
  margin: 0 auto;
  padding: var(--space-8);
}

/* ─────────────────────────────────────────────────────────────────────────────
   PAGE HEADER
───────────────────────────────────────────────────────────────────────────── */
.page-header {
  margin-bottom: var(--space-6);
}

.page-header h2 {
  font-size: 1.75rem;
  font-weight: 700;
  color: var(--text-strong);
  margin-bottom: var(--space-1);
  letter-spacing: -0.025em;
}

.page-header p {
  color: var(--text-muted);
  font-size: 0.9375rem;
}

/* ─────────────────────────────────────────────────────────────────────────────
   STATS GRID
───────────────────────────────────────────────────────────────────────────── */
.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: var(--space-5);
  margin-bottom: var(--space-6);
}

/* ─────────────────────────────────────────────────────────────────────────────
   STAT CARD
───────────────────────────────────────────────────────────────────────────── */
.stat-card {
  background: var(--surface);
  padding: var(--space-5) var(--space-6);
  border-radius: var(--radius-lg);
  border: 1px solid var(--border);
  box-shadow: var(--shadow-sm);
  transition: border-color 0.15s ease, box-shadow 0.15s ease;
}

.stat-card:hover {
  border-color: var(--border-strong);
  box-shadow: var(--shadow-md);
}

.stat-label {
  color: var(--text-muted);
  font-size: 0.6875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  margin-bottom: var(--space-2);
}

.stat-value {
  font-size: 2rem;
  font-weight: 700;
  color: var(--text-strong);
  letter-spacing: -0.025em;
  line-height: 1.1;
}

.stat-card.warning .stat-value { color: var(--warning); }
.stat-card.success .stat-value { color: var(--success); }
.stat-card.danger  .stat-value { color: var(--danger); }
.stat-card.info    .stat-value { color: var(--info); }

/* ─────────────────────────────────────────────────────────────────────────────
   CARD
───────────────────────────────────────────────────────────────────────────── */
.card {
  background: var(--surface);
  border-radius: var(--radius-lg);
  padding: var(--space-6);
  border: 1px solid var(--border);
  box-shadow: var(--shadow-sm);
  margin-bottom: var(--space-6);
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: var(--space-4);
  padding-bottom: var(--space-4);
  border-bottom: 1px solid var(--divider);
}

.card-title {
  font-size: 1rem;
  font-weight: 700;
  color: var(--text-strong);
  letter-spacing: -0.015em;
}

/* ─────────────────────────────────────────────────────────────────────────────
   TABLE
───────────────────────────────────────────────────────────────────────────── */
.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: var(--bg);
  border-top: 1px solid var(--border);
  border-bottom: 1px solid var(--border);
}

th {
  text-align: left;
  padding: var(--space-2) var(--space-3);
  font-weight: 600;
  color: var(--text-muted);
  font-size: 0.6875rem;
  text-transform: uppercase;
  letter-spacing: 0.06em;
}

td {
  padding: var(--space-3) var(--space-3);
  border-top: 1px solid var(--divider);
  color: var(--text-secondary);
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.12s ease;
}

tbody tr:hover {
  background: var(--bg);
}

/* ─────────────────────────────────────────────────────────────────────────────
   BADGES
───────────────────────────────────────────────────────────────────────────── */
.badge {
  display: inline-block;
  padding: 0.25rem 0.625rem;
  border-radius: var(--radius-sm);
  font-size: 0.6875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.04em;
}

.badge.success    { background: #d1fae5; color: #065f46; }
.badge.warning    { background: #fed7aa; color: #92400e; }
.badge.danger     { background: #fecaca; color: #991b1b; }
.badge.info       { background: #dbeafe; color: #1e40af; }
.badge.increasing { background: #d1fae5; color: #065f46; }
.badge.decreasing { background: #fecaca; color: #991b1b; }
.badge.stable     { background: #e0e7ff; color: #3730a3; }
.badge.high       { background: #fecaca; color: #991b1b; }
.badge.medium     { background: #fed7aa; color: #92400e; }
.badge.low        { background: #dbeafe; color: #1e40af; }

/* ─────────────────────────────────────────────────────────────────────────────
   LOADING / ERROR
───────────────────────────────────────────────────────────────────────────── */
.loading {
  text-align: center;
  padding: 3rem;
  color: var(--text-muted);
  font-size: 0.9375rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #991b1b;
  padding: var(--space-4);
  border-radius: var(--radius-md);
  margin: var(--space-4) 0;
  font-size: 0.9375rem;
}

/* ─────────────────────────────────────────────────────────────────────────────
   MOBILE OVERLAY
───────────────────────────────────────────────────────────────────────────── */
.sidebar-overlay {
  display: none;
  position: fixed;
  inset: 0;
  background: rgba(15, 23, 42, 0.4);
  z-index: 99;
  backdrop-filter: blur(1px);
}

/* ─────────────────────────────────────────────────────────────────────────────
   RESPONSIVE  ≤ 1024px  →  sidebar becomes off-canvas drawer
───────────────────────────────────────────────────────────────────────────── */
@media (max-width: 1024px) {
  .sidebar {
    position: fixed;
    left: 0;
    top: 0;
    transform: translateX(-100%);
    transition: transform 0.25s ease;
    z-index: 200;
  }

  .app.mobile-nav-open .sidebar {
    transform: translateX(0);
  }

  .app.mobile-nav-open .sidebar-overlay {
    display: block;
  }

  .topbar {
    display: flex;
  }

  .main-content {
    padding: var(--space-5) var(--space-4);
  }
}

/* ─────────────────────────────────────────────────────────────────────────────
   RESPONSIVE  ≤ 640px  →  single column, compact spacing
───────────────────────────────────────────────────────────────────────────── */
@media (max-width: 640px) {
  .main-content {
    padding: var(--space-4) var(--space-3);
  }

  .stats-grid {
    grid-template-columns: 1fr;
  }

  .page-header h2 {
    font-size: 1.375rem;
  }
}
</style>
