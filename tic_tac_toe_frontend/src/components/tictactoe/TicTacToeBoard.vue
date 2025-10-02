<script setup lang="ts">
import { computed, ref } from 'vue'

type Player = 'X' | 'O'
type Cell = Player | null

// Game state
const board = ref<Cell[]>(Array(9).fill(null))
const currentPlayer = ref<Player>('X')
const isGameOver = ref(false)
const winner = ref<Player | 'Draw' | null>(null)
const xScore = ref(0)
const oScore = ref(0)
const xName = ref('Player X')
const oName = ref('Player O')
const startingPlayer = ref<Player>('X')

// Computed helpers
const lines: number[][] = [
  [0, 1, 2],
  [3, 4, 5],
  [6, 7, 8],
  [0, 3, 6],
  [1, 4, 7],
  [2, 5, 8],
  [0, 4, 8],
  [2, 4, 6],
]

const winningLine = computed(() => {
  for (const [a, b, c] of lines) {
    if (board.value[a] && board.value[a] === board.value[b] && board.value[a] === board.value[c]) {
      return [a, b, c]
    }
  }
  return null
})

const movesMade = computed(() => board.value.filter(c => c !== null).length)

const statusText = computed(() => {
  if (isGameOver.value) {
    if (winner.value === 'Draw') return 'It’s a draw!'
    return `${winnerDisplayName.value} wins!`
  }
  return `${currentDisplayName.value}'s turn`
})

const currentDisplayName = computed(() => (currentPlayer.value === 'X' ? xName.value : oName.value))
const winnerDisplayName = computed(() => (winner.value === 'X' ? xName.value : oName.value))

// PUBLIC_INTERFACE
function resetBoard() {
  /** Reset the board but keep scores and starting player. */
  board.value = Array(9).fill(null)
  isGameOver.value = false
  winner.value = null
  currentPlayer.value = startingPlayer.value
}

// PUBLIC_INTERFACE
function newGame() {
  /** Start a new game and alternate the starting player for fairness. */
  startingPlayer.value = startingPlayer.value === 'X' ? 'O' : 'X'
  resetBoard()
}

function handleWin(player: Player) {
  winner.value = player
  isGameOver.value = true
  if (player === 'X') xScore.value += 1
  if (player === 'O') oScore.value += 1
}

function checkGameState() {
  const wl = winningLine.value
  if (wl) {
    handleWin(board.value[wl[0]] as Player)
    return
  }

  if (board.value.every(c => c !== null)) {
    winner.value = 'Draw'
    isGameOver.value = true
  }
}

// PUBLIC_INTERFACE
function makeMove(index: number) {
  /** Make a move for the current player if valid. */
  if (isGameOver.value || board.value[index] !== null) return
  board.value[index] = currentPlayer.value
  checkGameState()
  if (!isGameOver.value) {
    currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
  }
}
</script>

<template>
  <div class="game">
    <!-- Header / Names -->
    <div class="players">
      <div class="player" :class="{ active: !isGameOver && currentPlayer === 'X' }">
        <span class="badge x">X</span>
        <input
          aria-label="Player X name"
          v-model="xName"
          class="name-input"
          maxlength="20"
        />
      </div>
      <div class="score">
        <span class="x-score">{{ xScore }}</span>
        <span class="sep">:</span>
        <span class="o-score">{{ oScore }}</span>
      </div>
      <div class="player" :class="{ active: !isGameOver && currentPlayer === 'O' }">
        <span class="badge o">O</span>
        <input
          aria-label="Player O name"
          v-model="oName"
          class="name-input"
          maxlength="20"
        />
      </div>
    </div>

    <!-- Board -->
    <div class="board" role="grid" aria-label="Tic Tac Toe board">
      <button
        v-for="(cell, idx) in board"
        :key="idx"
        role="gridcell"
        class="cell"
        :class="{
          x: cell === 'X',
          o: cell === 'O',
          win: winningLine?.includes(idx) ?? false
        }"
        :aria-label="`Cell ${idx + 1}`"
        @click="makeMove(idx)"
      >
        <span v-if="cell" class="mark">{{ cell }}</span>
      </button>
    </div>

    <!-- Status -->
    <div class="status-row" :class="{ over: isGameOver }">
      <div class="status">
        <span class="dot" :class="{ x: !isGameOver && currentPlayer === 'X', o: !isGameOver && currentPlayer === 'O', over: isGameOver }"></span>
        <span class="text">{{ statusText }}</span>
        <span v-if="movesMade" class="moves">Moves: {{ movesMade }}</span>
      </div>
    </div>

    <!-- Controls -->
    <div class="controls">
      <button class="btn outline" @click="resetBoard" :disabled="movesMade === 0 && !isGameOver">
        Reset
      </button>
      <button class="btn primary" @click="newGame">
        New Game
      </button>
    </div>

    <!-- Tips -->
    <p class="tip">
      Pro tip: alternate starting player each game for fairness. Names are editable.
    </p>
  </div>
</template>

<style scoped>
.game {
  display: grid;
  gap: 16px;
}

/* Players / Scores */
.players {
  display: grid;
  grid-template-columns: 1fr auto 1fr;
  align-items: center;
  gap: 10px;
}

.player {
  display: flex;
  align-items: center;
  gap: 8px;
  background: linear-gradient(180deg, rgba(37,99,235,0.06), rgba(37,99,235,0.02));
  padding: 10px 12px;
  border-radius: var(--radius-md);
  border: 1px solid rgba(17,24,39,0.06);
  transition: box-shadow .2s ease, border-color .2s ease;
}

.player.active {
  border-color: rgba(37,99,235,0.35);
  box-shadow: 0 8px 24px rgba(37,99,235,0.14);
}

.badge {
  display: inline-grid;
  place-items: center;
  width: 28px;
  height: 28px;
  border-radius: 8px;
  font-weight: 800;
  letter-spacing: .4px;
  border: 1px solid;
}

.badge.x {
  color: var(--ocean-primary);
  background: rgba(37,99,235,0.10);
  border-color: rgba(37,99,235,0.35);
}

.badge.o {
  color: var(--ocean-secondary);
  background: rgba(245,158,11,0.10);
  border-color: rgba(245,158,11,0.35);
}

.name-input {
  border: none;
  background: transparent;
  padding: 6px 8px;
  border-radius: 8px;
  min-width: 0;
  width: 100%;
  outline: none;
  font-weight: 600;
  color: var(--ocean-text);
}

.name-input:focus {
  box-shadow: var(--ring);
  background: rgba(59,130,246,0.06);
}

.score {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  border-radius: var(--radius-md);
  background: var(--ocean-surface);
  border: 1px solid rgba(17,24,39,0.06);
  box-shadow: var(--shadow-sm);
  font-weight: 800;
}

.x-score { color: var(--ocean-primary); }
.o-score { color: var(--ocean-secondary); }
.sep { color: var(--ocean-muted); opacity: .75; }

/* Board */
.board {
  width: 100%;
  aspect-ratio: 1 / 1;
  display: grid;
  gap: 10px;
  grid-template-columns: repeat(3, 1fr);
  background: linear-gradient(180deg, rgba(17,24,39,0.02), rgba(17,24,39,0.02));
  padding: clamp(6px, 1.4vw, 10px);
  border-radius: var(--radius-lg);
  border: 1px solid rgba(17,24,39,0.06);
}

.cell {
  border: 1px solid rgba(17,24,39,0.06);
  border-radius: 14px;
  background: var(--ocean-surface);
  display: grid;
  place-items: center;
  font-weight: 900;
  font-size: clamp(2.2rem, 9.5vw, 3.4rem);
  transition: transform .12s ease, box-shadow .12s ease, border-color .12s ease, background .12s ease;
  box-shadow: var(--shadow-sm);
  user-select: none;
}

.cell:hover {
  transform: translateY(-2px);
  border-color: rgba(37,99,235,0.30);
  box-shadow: 0 10px 22px rgba(37,99,235,0.16);
}

.cell:active {
  transform: translateY(0px) scale(0.995);
}

.cell.x .mark { color: var(--ocean-primary); text-shadow: 0 6px 16px rgba(37,99,235,0.18); }
.cell.o .mark { color: var(--ocean-secondary); text-shadow: 0 6px 16px rgba(245,158,11,0.18); }

.cell.win {
  background: linear-gradient(180deg, rgba(37,99,235,0.10), rgba(245,158,11,0.10));
  border-color: rgba(37,99,235,0.40);
  box-shadow: 0 14px 34px rgba(37,99,235,0.22);
}

/* Status row */
.status-row {
  display: flex;
  align-items: center;
  justify-content: center;
}

.status {
  display: inline-flex;
  align-items: center;
  gap: 10px;
  padding: 10px 12px;
  background: var(--ocean-surface);
  border: 1px solid rgba(17,24,39,0.06);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-sm);
}

.status .text {
  font-weight: 700;
}

.status .moves {
  color: var(--ocean-muted);
  font-weight: 600;
}

.dot {
  width: 10px;
  height: 10px;
  border-radius: 999px;
  background: rgba(17,24,39,0.14);
}

.dot.x { background: var(--ocean-primary); }
.dot.o { background: var(--ocean-secondary); }
.dot.over { background: rgba(17,24,39,0.24); }

/* Controls */
.controls {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 10px;
}

.btn {
  padding: 12px 14px;
  border-radius: 12px;
  border: 1px solid transparent;
  font-weight: 800;
  transition: transform .12s ease, box-shadow .12s ease, background .12s ease, border-color .12s ease;
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.btn.primary {
  background: linear-gradient(180deg, rgba(37,99,235,0.90), rgba(37,99,235,0.85));
  color: white;
  box-shadow: 0 10px 24px rgba(37,99,235,0.35);
}

.btn.primary:hover {
  transform: translateY(-2px);
  box-shadow: 0 18px 40px rgba(37,99,235,0.40);
}

.btn.outline {
  background: var(--ocean-surface);
  border-color: rgba(37,99,235,0.35);
  color: var(--ocean-primary);
  box-shadow: var(--shadow-sm);
}

.btn.outline:hover {
  transform: translateY(-2px);
  box-shadow: 0 12px 28px rgba(37,99,235,0.18);
}

/* Tip */
.tip {
  margin: 4px 2px 0;
  color: var(--ocean-muted);
  font-size: 0.9rem;
  text-align: center;
}

/* Responsive */
@media (max-width: 420px) {
  .players {
    grid-template-columns: 1fr;
    gap: 8px;
  }
  .score {
    justify-content: center;
  }
  .controls {
    grid-template-columns: 1fr;
  }
}
</style>
