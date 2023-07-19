<template>
  <div class="border border-[#39bcd4] py-6 px-10 shadow-2xl">
    <!-- game results -->
    <div class="flex justify-between">
      <div class="flex flex-col items-center text-[#39bcd4]">
        <OIcon class="w-3.5 h-3.5" />
        <span class="font-bold">{{ gameResultStats.o }} wins</span>
      </div>
      <div class="flex flex-col items-center text-[#3989d4]">
        <XIcon class="w-3.5 h-3.5" />
        <span class="font-bold">{{ gameResultStats.x }} wins</span>
      </div>
      <div class="flex flex-col items-center text-[#9d9d9d]">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 -1 24 24" class="w-4 h-4">
          <path fill="currentColor" fill-rule="evenodd" d="m6.17 4.347 3.779 11.337a1 1 0 0 1-.502 1.21l-.87.435a8 8 0 0 1-7.155 0l-.87-.435a1 1 0 0 1-.5-1.21L3.711 4.7c-.505.085-1.01.178-1.516.28a1 1 0 1 1-.392-1.962c3.064-.613 6.13-.95 9.196-1.01V1a1 1 0 0 1 2 0v1.01c3.066.06 6.132.396 9.196 1.01a1 1 0 0 1-.392 1.96 51.677 51.677 0 0 0-1.516-.28l3.66 10.984a1 1 0 0 1-.5 1.21l-.87.435a8 8 0 0 1-7.156 0l-.87-.435a1 1 0 0 1-.5-1.21L17.83 4.347A49.393 49.393 0 0 0 13 4.01V20h3a1 1 0 0 1 0 2H8a1 1 0 1 1 0-2h3V4.01c-1.61.033-3.22.145-4.83.337zm15.607 11.146L19 7.163l-2.777 8.33.094.047a6 6 0 0 0 5.366 0l.094-.047zM5 7.163l-2.777 8.33.094.047a6 6 0 0 0 5.366 0l.094-.047L5 7.163z" clip-rule="evenodd"/>
        </svg>
        <span class="font-bold -mt-0.5">{{ gameResultStats.draw }} draws</span>
      </div>
    </div>

    <!-- board -->
    <div class="mt-8">
      <div v-for="(row, rowIndex) in board" class="flex border-b-2 last:border-b-0 border-stone-300">
        <div v-for="(square, columnIndex) in row" class="border-r-2 last:border-r-0 border-stone-300 p-0.5">
            <button
              type="button"
              class="w-16 h-16 flex items-center justify-center"
              @click="onMove(rowIndex, columnIndex)"
            >
              <span v-if="square === 'x'" class="w-full h-full flex items-center justify-center">
                <XIcon class="w-3/5 text-[#3989d4]" />
              </span>
              <span v-if="square === 'o'" class="w-full h-full flex items-center justify-center">
                <OIcon class="w-3/5 text-[#39bcd4]" />
              </span>
            </button>
        </div>
      </div>
    </div>

    <!-- game status -->
    <div class="flex justify-center mt-10">
      <div class="flex items-center justify-center border border-stone-200 rounded-full px-3 py-1 min-w-[90px]">
        <template v-if="gameIsOver && winnerIsDefined">
          <XIcon v-if="lastMove.sign === 'x'" class="w-3 h-3 text-[#3989d4]" />
          <OIcon v-if="lastMove.sign === 'o'" class="w-3 h-3 text-[#39bcd4]" />
          <span class="uppercase text-slate-500 ml-1.5">wins</span>
          <span>{{ randomPrize() }}</span>
        </template>
        <template v-if="gameIsOver && !winnerIsDefined">
          <span class="uppercase text-slate-500 ">draw</span>
          <XIcon class="w-3 h-3 text-[#3989d4] ml-1.5" />
          <OIcon class="w-3 h-3 text-[#39bcd4] ml-1" />
        </template>
        <template v-if="!gameIsOver">
          <XIcon class="w-3 h-3" :class="[ turn === 'x' ? 'text-[#3989d4]' : 'text-stone-200' ]" />
          <span class="uppercase text-slate-500 mx-2">turn</span>
          <OIcon class="w-3 h-3" :class="[ turn === 'o' ? 'text-[#39bcd4] ]' : 'text-stone-200' ]" />
        </template>
      </div>
    </div>

    <!-- extra buttons -->
    <div class="mt-8 flex items-center justify-center space-x-4">
      <div class="flex rounded-full border border-[#e0e5e6] p-0.5">
        <button
          type="button" class="rounded-full bg-[#a4b5b8] text-[#e0e5e6] hover:text-slate-100 transition-colors duration-200 p-2"
          title="Reset game"
          @click="onReset"
        >
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="w-5 h-5">
            <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m20.956 9.748 1.298-3.618M20.956 9.748l-3.793-.625M20.162 8.282A8.749 8.749 0 0 0 3.48 10.85M3.546 14.252 2.247 17.87M3.546 14.252l3.792.625M4.34 15.718a8.749 8.749 0 0 0 16.683-2.567"/>
          </svg>
        </button>
      </div>

      <span class="border border-stone-200 rounded-full px-3 py-1.5 text-[#999999] text-xs font-bold uppercase">
        2 players
      </span>

      <div class="flex rounded-full border border-[#e0e5e6] p-0.5">
        <a href="https://github.com/MirasMustimov/tic-tac-toe-demo" target="blank" title="See Github page" class="rounded-full bg-[#a4b5b8] text-[#e0e5e6] hover:text-slate-100 transition-colors duration-200 p-2">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 -0.5 24 24" class="w-5 h-5">
            <path fill="currentColor" fill-rule="evenodd" d="M12.205 0a11.499 11.499 0 0 0-3.636 22.412c.58.106.784-.254.784-.558V19.9c-3.212.699-3.89-1.546-3.89-1.546a3.077 3.077 0 0 0-1.277-1.687c-1.038-.706.085-.706.085-.706a2.421 2.421 0 0 1 1.757 1.186 2.457 2.457 0 0 0 3.346.96c.044-.58.295-1.127.706-1.539-2.555-.29-5.238-1.278-5.238-5.647a4.447 4.447 0 0 1 1.18-3.085 4.193 4.193 0 0 1 .112-3.042s.967-.31 3.162 1.179a10.878 10.878 0 0 1 5.76 0c2.196-1.49 3.156-1.18 3.156-1.18.423.955.473 2.033.14 3.022a4.447 4.447 0 0 1 1.18 3.085c0 4.419-2.69 5.386-5.252 5.647.556.559.842 1.331.784 2.117v3.156c0 .374.204.663.79.55A11.499 11.499 0 0 0 12.204 0z" clip-rule="evenodd"/>
          </svg>
        </a>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'

import XIcon from './Icons/XIcon.vue'
import OIcon from './Icons/OIcon.vue'

let boardSize = 3
let moves = ref([])

let gameResultStats = ref({
  x: 0,
  o: 0,
  draw: 0
})

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

let turn = computed(() => {
  if (lastMove.value === null) {
    return 'x'
  }

  return lastMove.value.sign === 'x' ? 'o' : 'x'
})

let lastMove = computed(() => {
  if (moves.value.length === 0) {
    return null
  }

  return moves.value[moves.value.length - 1]
})

let winnerIsDefined = computed(() => {
  if (lastMove.value === null) {
    return false
  }

  let winsByHorizontal = board.value[lastMove.value.row].every(square => square === lastMove.value.sign)

  let winsByVertical = board.value.every(row => row[lastMove.value.column] === lastMove.value.sign)

  let winsByDiagonal = board.value.every((row, index) => row[0 + index] === lastMove.value.sign) ||
                       board.value.every((row, index) => row[board.value.length - 1 - index] === lastMove.value.sign)

  return winsByHorizontal || winsByVertical || winsByDiagonal
})

let gameIsOver = computed(() => {
  return winnerIsDefined.value || moves.value.length >= Math.pow(boardSize, 2)
})

let onMove = (row: number, column: number) => {
  if (gameIsOver.value) {
    alert('Game is over')
    return
  }

  if (board.value[row][column] !== 'blank') {
    alert('Square is taken')
    return
  }

  moves.value.push({
    row: row,
    column: column,
    sign: (moves.value.length % 2 === 0 ? 'x' : 'o')
  })

  if (gameIsOver.value) {
    winnerIsDefined.value
      ? gameResultStats.value[lastMove.value.sign]++
      : gameResultStats.value.draw++
  }
}

let onReset = () => {
    moves.value = []
}

let randomPrize = () => {
  let prizes = ['🎈', '🧁', '🍭', '🎮', '🪁', '🍉', '🍫']
  return prizes[Math.floor(Math.random()*prizes.length)]
}
</script>
