<script setup lang="ts">
import { RouterView } from 'vue-router'
</script>

<template>
  <div class="app-shell">
    <header class="app-header">
      <div class="brand">
        <div class="logo">TTT</div>
        <div class="titles">
          <h1>Tic Tac Toe</h1>
          <p>Ocean Professional</p>
        </div>
      </div>
    </header>

    <main class="app-main">
      <RouterView />
    </main>

    <footer class="app-footer">
      <span>Built with Vue 3 + Vite</span>
    </footer>
  </div>
</template>

<style scoped>
:root {
  --ocean-primary: #2563EB;
  --ocean-secondary: #F59E0B;
  --ocean-error: #EF4444;
  --ocean-surface: #ffffff;
  --ocean-bg: #f9fafb;
  --ocean-text: #111827;
  --ocean-muted: #6b7280;
}

.app-shell {
  min-height: 100vh;
  display: grid;
  grid-template-rows: auto 1fr auto;
  background:
    radial-gradient(1000px 600px at 50% -200px, rgba(59,130,246,0.08), rgba(249,250,251,0)) no-repeat,
    var(--ocean-bg);
  color: var(--ocean-text);
}

.app-header {
  max-width: 1100px;
  margin: 0 auto;
  width: 100%;
  padding: 1.25rem 1rem 0.5rem;
}

.brand {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.logo {
  width: 42px;
  height: 42px;
  border-radius: 12px;
  background: linear-gradient(135deg, rgba(37, 99, 235, 0.12), rgba(245, 158, 11, 0.12));
  display: grid;
  place-items: center;
  color: var(--ocean-primary);
  font-weight: 800;
  letter-spacing: 0.5px;
  border: 1px solid rgba(37,99,235,0.22);
  box-shadow: 0 6px 16px rgba(37,99,235,0.12);
}

.titles h1 {
  margin: 0;
  font-size: 1.25rem;
  line-height: 1.15;
}

.titles p {
  margin: 0;
  color: var(--ocean-muted);
  font-size: 0.85rem;
}

.app-main {
  display: grid;
  place-items: center;
  padding: 1rem;
}

.app-footer {
  font-size: 0.85rem;
  color: var(--ocean-muted);
  text-align: center;
  padding: 1rem;
}
</style>
