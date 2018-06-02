<template>
<div class='wrapper'>
  <div class='btn-wrapper'>
    <v-btn :small="true" @click="tickHandler">Tick</v-btn>
    <v-btn :small="true" @click="clear">Clear</v-btn>
    <v-btn v-if="this.tick" light color="red darken-2" small @click="goHandler">Stop</v-btn>
    <v-btn v-else color="green" small @click="goHandler">Go</v-btn>
    <v-btn small @click="addCol">Add Col</v-btn>
    <v-btn small @click="addRow">Add Row</v-btn>
    <v-text-field label="Width" v-model.number="width"/>
    <v-text-field label="Height" v-model="height"/>
  </div>
  <div class='container-wrapper'>
    <div class='life-container'>
      <div class='col' v-for="(col, rindex) in cells" :key="`col-${rindex}`">
          <template v-for="(cell, cindex) in col" >
            <div class="cell" 
                :class="cell ? 'alive' : 'dead'"
                :key="`r-${rindex}-c-${cindex}`"
                :data-x="`${rindex}`"
                :data-y="`${cindex}`"
                @click="toggleCell(rindex, cindex)"
                @mouseover="mouseHandler($event, rindex, cindex)"
            >
            </div>
          </template>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    startWidth: Number,
    startHeight: Number
  },

  data() {
    return {
      cells: [],
      tick: false,
      width: 0,
      height: 0
    };
  },

  mounted() {
    this.width = this.startWidth;
    this.height = this.startHeight;
    this.initCells();
  },

  watch: {
    width() {
      this.applyWidth();
    },
    height() {
      this.applyHeight();
    }
  },

  methods: {
    applyWidth: _.debounce(function() {
      this.cells = this.newCells(this.width, this.height);
    }, 500),
    applyHeight: _.debounce(function() {
      this.cells = this.newCells(this.width, this.height);
    }, 500),
    clear() {
      this.cells = this.newCells();
    },
    mouseHandler(event, x, y) {
      if (event.buttons && !this.cellVal(x, y)) {
        this.toggleCell(x, y);
      }
    },
    addCol() {
      this.cells = this.newCells(this.width + 1, this.height);
    },
    addRow() {
      this.cells = this.newCells(this.width, this.height + 1);
    },
    newCells(width = this.width, height = this.height) {
      this.width = width;
      this.height = height;
      return Array(width)
        .fill(0)
        .map(() => Array(height).fill(0));
    },
    initCells() {
      this.cells = this.newCells();
    },
    toggleCell(x, y) {
      var col = this.cells[x];
      col[y] = col[y] ? 0 : 1;
      this.cells.splice(x, 1, col);
    },
    goHandler() {
      if (!this.tick) {
        this.tick = window.setInterval(() => {
          this.tickHandler();
        }, 100);
      } else {
        window.clearInterval(this.tick);
        this.tick = false;
      }
    },
    liveNeighbors(x, y) {
      var neighbors = 0;
      neighbors += this.cellVal(x, y - 1);
      neighbors += this.cellVal(x + 1, y - 1);
      neighbors += this.cellVal(x + 1, y);
      neighbors += this.cellVal(x + 1, y + 1);
      neighbors += this.cellVal(x, y + 1);
      neighbors += this.cellVal(x - 1, y + 1);
      neighbors += this.cellVal(x - 1, y);
      neighbors += this.cellVal(x - 1, y - 1);
      return neighbors;
    },
    tickHandler() {
      var n = this.newCells();

      var neighbors = 0;
      for (var x = 0; x < this.width; x++) {
        for (var y = 0; y < this.height; y++) {
          neighbors = this.liveNeighbors(x, y);
          if (this.cells[x][y]) {
            if (neighbors < 2) {
              n[x][y] = 0;
            } else if (neighbors === 2 || neighbors === 3) {
              n[x][y] = 1;
            } else if (neighbors > 3) {
              n[x][y] = 0;
            }
          } else {
            if (neighbors === 3) {
              n[x][y] = 1;
            }
          }
          neighbors = 0;
        }
      }

      this.cells = n;
    },
    cellVal(x, y) {
      if (x < 0) {
        x = this.width - 1;
      } else if (x === this.width) {
        x = 0;
      }
      if (y < 0) {
        y = this.height - 1;
      } else if (y === this.height) {
        y = 0;
      }

      return this.cells[x][y];
    }
  }
};
</script>

<style>
.wrapper {
  display: flex;
  justify-content: center;
}

.life-container {
  display: flex;
  justify-content: center;
}

.btn-wrapper {
  display: flex;
  flex-direction: column;
}

.cell {
  height: 10px;
  width: 10px;

  border-left: 1px solid black;
  border-top: 1px solid black;

  background-color: white;
}

.cell.alive {
  background-color: black;
}

.col {
  border-bottom: 1px solid black;
}

.col:last-of-type {
  border-right: 1px solid black;
}
</style>
