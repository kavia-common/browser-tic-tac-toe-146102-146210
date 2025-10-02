<script setup lang="ts">
import TicTacToeBoard from '@/components/tictactoe/TicTacToeBoard.vue'
</script>

<template>
  <main class="home">
    <TicTacToeBoard />
  </main>
</template>

<style scoped>
.home {
  display: grid;
  place-items: center;
  padding: clamp(12px, 4vw, 24px);
  width: 100%;
}
</style>
