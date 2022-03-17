<script setup lang="ts">
interface BlockState {
  x: number
  y: number
  adjacentMines: number
  reveabled: boolean
  mine?: boolean
  flagged?: boolean
}
const HEIGHT = 10
const WIDTH = 10
const state = reactive(
  Array.from({ length: HEIGHT }, (_, y) =>
    Array.from({ length: WIDTH },
      (_, x): BlockState => ({ x, y, adjacentMines: 0, reveabled: false })),
  ))

function generateMines() {
  for (const row of state) {
    for (const block of row)
      block.mine = Math.random() < 0.3
  }
}

const directions = [
  [1, 1],
  [1, 0],
  [1, -1],
  [0, -1],
  [0, 1],
  [-1, 1],
  [-1, 0],
  [-1, -1],
]

const numberColors = [
  'text-transparent',
  'text-blue-500',
  'text-green',
  'text-yellow',
  'text-orange',
  'text-red',
  'text-purple',
  'text-pink',
]

function updateNumbers() {
  state.forEach((raw, y) => {
    raw.forEach((block, x) => {
      if (block.mine)
        return
      directions.forEach(([dx, dy]) => {
        const x2 = dx + x
        const y2 = dy + y
        if (x2 < 0 || x2 >= WIDTH || y2 < 0 || y2 >= HEIGHT)
          return

        if (state[y2][x2].mine)
          block.adjacentMines += 1
      })
    })
  })
}

function onClick(x: number, y: number) {
  console.log(x, y)
}
function getBlockClass(item: BlockState) {
  return item.mine ? 'bg-red-500/50' : numberColors[item.adjacentMines]
}
generateMines()
updateNumbers()
</script>

<template>
  <div>
    minesweeper
    <div p5>
      <div
        v-for="row, y in state"
        :key="y" flex="~"
        items-center justify-center
      >
        <button
          v-for="item, x in row"
          :key="x"
          w-10 h-10 m="0.5"
          border="1 gray-500/30"
          hover="bg-gray/20"
          flex="~"
          items-center justify-center
          :class="getBlockClass(item)"
          @click="onClick(x, y)"
        >
          <div v-if="item.mine" i-mdi-mine />
          <div v-else>
            {{ item.adjacentMines }}
          </div>
        </button>
      </div>
    </div>
  </div>
</template>
