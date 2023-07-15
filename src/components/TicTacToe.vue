<template>
  <div>
    <h2 class="text-center mb-3 italic">Tic Tac Toe</h2>
    <section v-for="(row, rowIndex) in board" class="flex">
      <button
        v-for="(square, squareIndex) in row"
        type="button"
        class="w-12 h-12 flex items-center justify-center border"
        @click="onMove(rowIndex, squareIndex)"
      >
        <span v-if="square === 'o' || square === 'x'">
          {{ square }}
        </span>
      </button>
    </section>

    <h5 v-if="gameIsOver" class="mb-2">
      Game is over:
      <span v-if="winner"> {{ winner }} wins.</span>
      <span v-else>tie.</span>
    </h5>

    <div class="text-center">
      <button type="button" class="border-b" @click="onRestart">
        Restart
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

let board = ref([
  [ 'blank', 'blank', 'blank' ],
  [ 'blank', 'blank', 'blank' ],
  [ 'blank', 'blank', 'blank' ]
])

let moveCount = ref(0)
let winner = ref(null)

let turn = computed(() => {
  return moveCount.value % 2 === 0 ? 'x' : 'o'
})

let gameIsOver = computed(() => {
  return winner.value !== null || moveCount.value >= 9
})

let onMove = (rowIndex: number, squareIndex: number) => {
  if (gameIsOver.value) {
    alert('Game is over')
    return
  }

  if (board.value[rowIndex][squareIndex] !== 'blank') {
    alert('Cant use this square')
    return
  }

  board.value[rowIndex][squareIndex] = turn.value

  if (moveWins(rowIndex, squareIndex, turn.value)) {
    winner.value = turn.value
  }

  moveCount.value++
}

let moveWins = (rowIndex: number, squareIndex: number, turn: ('x' | 'o')) => {
  let winsByHorizontal = board.value[rowIndex].every(square => square === turn)

  let winsByVertical = board.value.every(row => row[squareIndex] === turn)

  let winsByDiagonal = board.value.every((row, index) => row[0 + index] === turn) ||
                       board.value.every((row, index) => row[board.value.length - 1 - index] === turn)

  return winsByHorizontal || winsByVertical || winsByDiagonal
}

let onRestart = () => {
    board.value = [
      [ 'blank', 'blank', 'blank' ],
      [ 'blank', 'blank', 'blank' ],
      [ 'blank', 'blank', 'blank' ]
    ]
    moveCount.value = 0
    winner.value = null
}
</script>
