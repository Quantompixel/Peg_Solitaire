<template>
  <div @mousemove="updateMousePosition"
       @mouseup="grabbedPeg !== undefined ? dropPeg(hoveredHole.row, hoveredHole.column, grabbedPeg) : log('Cant drop peg here.')"
       ref="board"
       id="board">
    <div v-for="arr in map" class="row" ref="rows">
      <div v-for="{row, column, hasPeg, color, wall} in arr" class="slot">
        <Wall v-if="wall"></Wall>
        <Hole v-else
              :row="row"
              :column="column"
              :has-peg="hasPeg"
              :color="color"
              @pegGrabbed="(row, column) => pegGrabbed(row, column)"
              @holeHovered="(row, column) => holeHovered(row, column)">
        </Hole>
      </div>
    </div>

    <Peg :is-visible="cosmeticPegSpawned"
         :mouse-x="mouseX"
         :mouse-y="mouseY"
         :width="widthCosmeticPeg"
         :height="heightCosmeticPeg">
    </Peg>
  </div>
</template>

<script>
import Peg from "./Peg.vue";
import Hole from "./Hole.vue";
import Wall from "./Wall.vue";

export default {
  name: "Board",
  components: {Wall, Hole, Peg},
  created() {
    this.initMap()
  },
  mounted() {
    this.recalculateSize()
  },
  data() {
    return {
      grid: [
        [2, 2, 2, 1, 1, 1, 2, 2, 2],
        [2, 2, 2, 1, 1, 1, 2, 2, 2],
        [2, 2, 2, 1, 1, 1, 2, 2, 2],
        [1, 1, 1, 1, 1, 1, 1, 1, 1],
        [1, 1, 1, 1, 0, 1, 1, 1, 1],
        [1, 1, 1, 1, 1, 1, 1, 1, 1],
        [2, 2, 2, 1, 1, 1, 2, 2, 2],
        [2, 2, 2, 1, 1, 1, 2, 2, 2],
        [2, 2, 2, 1, 1, 1, 2, 2, 2],
      ],
      map: [],
      mouseX: 0,
      mouseY: 0,
      widthCosmeticPeg: 0,
      heightCosmeticPeg: 0,
      defaultColor: 'black',
      grabbedPeg: undefined,
      hoveredHole: undefined,
      cosmeticPegSpawned: false
    }
  },
  methods: {
    initMap() {
      this.grid.forEach((row, i) => {
        let newRow = [];

        row.forEach((value, k) => {
          newRow.push(
            {
              row: i,
              column: k,
              hasPeg: Boolean(value),
              color: 'black',
              wall: value >= 2,
            }
          )
        })

        this.map.push(newRow)
      })
    },
    updateMousePosition(event) {
      this.mouseX = event.clientX
      this.mouseY = event.clientY
    },
    pegGrabbed(row, column) {
      this.spawnCosmeticPeg()

      this.grabbedPeg = this.map[row][column]
      this.grabbedPeg.hasPeg = false

      this.recalculateColor(row, column, this.defaultColor)
    },
    holeHovered(row, column) {
      this.hoveredHole = this.map[row][column]
      // this.log(`${this.hoveredHole.row} ${this.hoveredHole.column}`)
    },
    dropPeg(atRow, atColumn, pegToDrop) {
      this.deSpawnCosmeticPeg()

      if (this.isAllowedToDrop(pegToDrop.row, pegToDrop.column, atRow, atColumn)) {
        const middlePeg = this.getMiddleHole(pegToDrop.row, pegToDrop.column, atRow, atColumn)

        this.map[atRow][atColumn].hasPeg = true
        pegToDrop.hasPeg = false
        middlePeg.hasPeg = false

        this.grabbedPeg = undefined
      } else {
        pegToDrop.hasPeg = true
      }

      this.removeColor(this.defaultColor)
    },
    isAllowedToDrop(fromRow, fromColumn, atRow, atColumn) {
      if (this.map[atRow][atColumn].hasPeg) return false
      else if (atRow >= this.map.length) return false
      else if (atColumn >= this.map[atColumn].length) return false

      const diffRow = fromRow - atRow
      const diffColumn = fromColumn - atColumn

      if ((Math.abs(diffRow) === 2 && diffColumn === 0) || (Math.abs(diffColumn) === 2 && diffRow === 0)) {
        const middleHole = this.getMiddleHole(fromRow, fromColumn, atRow, atColumn)

        // if middle hole has peg
        if (middleHole.hasPeg) return true
      } else {
        return false
      }
    },
    getMiddleHole(fromRow, fromColumn, atRow, atColumn) {
      const diffRow = fromRow - atRow
      const diffColumn = fromColumn - atColumn

      const middleHoleRow = atRow + diffRow / 2
      const middleHoleColumn = atColumn + diffColumn / 2

      return this.map[middleHoleRow][middleHoleColumn];
    },
    spawnCosmeticPeg() {
      this.cosmeticPegSpawned = true
    },
    deSpawnCosmeticPeg() {
      this.cosmeticPegSpawned = false
    },
    recalculateSize() {
      console.log("Recalculating size ...")

      // fits rows into the bounding box of the board
      this.$refs.rows.forEach((row) => {
        row.style.height = `${100 / this.map.length}%`
      })

      // scales cosmetic peg to the right size
      const widthBoard = this.$refs.board.clientWidth
      const heightBoard = this.$refs.board.clientHeight
      this.widthCosmeticPeg = widthBoard / this.map[0].length * 0.9 * 0.8
      this.heightCosmeticPeg = heightBoard / this.map.length * 0.9 * 0.8
    },
    recalculateColor(fromRow, fromColumn, defaultColor = this.defaultColor) {
      for (let array of this.map) {
        for (let hole of array) {
          if (!hole.wall) {
            hole.color = this.isAllowedToDrop(fromRow, fromColumn, hole.row, hole.column) ? 'green' : 'red'
          }
        }
      }

      this.grabbedPeg.color = defaultColor
    },
    removeColor(defaultColor = this.defaultColor) {
      for (let array of this.map) {
        for (let hole of array) {
          if (!hole.wall) {
            hole.color = defaultColor
          }
        }
      }
    },
    log(msg) {
      console.log(msg)
    }
  }
}
</script>

<style scoped>
#board {
  width: 70vh;
  height: 70vh;
}

.row {
  width: 100%;
  display: flex;
  flex-direction: row;
}

.slot {
  width: 100%;
  height: 100%;
}

@media screen and (orientation: portrait){
  #board {
    height: 90vw;
    width: 90vw;
  }
}

</style>
