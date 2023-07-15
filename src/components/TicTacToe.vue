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

let boardSize = 3
let moves = ref([])

let board = computed(() => {
  let resultBoard = []

  for (let row = 0; row < boardSize; row++) {
    resultBoard[row] = []
    for (let column = 0; column < boardSize; column++) {
      resultBoard[row][column] = 'blank'
    }
  }

  for (let i = 0; i < moves.value.length; i++) {
    let move = moves.value[i]
    resultBoard[move.row][move.column] = move.sign
  }

  return resultBoard
})

let lastMove = computed(() => {
  if (moves.value.length === 0) {
    return null
  }

  return moves.value[moves.value.length - 1]
})

let winner = computed(() => {
  if (lastMove.value === null) {
    return null
  }

  let winsByHorizontal = board.value[lastMove.value.row].every(square => square === lastMove.value.sign)

  let winsByVertical = board.value.every(row => row[lastMove.value.column] === lastMove.value.sign)

  let winsByDiagonal = board.value.every((row, index) => row[0 + index] === lastMove.value.sign) ||
                       board.value.every((row, index) => row[board.value.length - 1 - index] === lastMove.value.sign)

  if (winsByHorizontal || winsByVertical || winsByDiagonal) {
    return lastMove.value.sign
  }

  return null
})

let gameIsOver = computed(() => {
  return winner.value !== null || moves.value.length >= 9
})

let onMove = (row: number, column: number) => {
  if (gameIsOver.value) {
    alert('Game is over')
    return
  }

  if (board.value[row][column] !== 'blank') {
    alert('Cant use this square')
    return
  }

  moves.value.push({
    row: row,
    column: column,
    sign: (moves.value.length % 2 === 0 ? 'x' : 'o')
  })
}

let onRestart = () => {
    moves.value = []
}
</script>
