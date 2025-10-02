<template>
  <div class="about">
    <div class="panel">
      <h1>About</h1>
      <p>This is a simple two-player Tic Tac Toe built with Vue 3 & Vite.</p>
    </div>
  </div>
</template>

<style scoped>
.about {
  min-height: 60vh;
  display: grid;
  place-items: center;
  padding: 24px;
}

.panel {
  background: var(--ocean-surface);
  border: 1px solid rgba(17,24,39,0.06);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-md);
  padding: 20px 22px;
  max-width: 720px;
}

h1 {
  margin-bottom: 8px;
}

p {
  color: var(--ocean-muted);
  font-weight: 600;
}
</style>
