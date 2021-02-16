<template>
  <div class="game-container">
    <div class="game-board">
      <div class="game-row" 
        v-for="(row, rowIndex) in gameValues" 
        :key="rowIndex">
        <div class="game-cell" 
          v-for="(cell, cellIndex) in gameValues[rowIndex]" 
          :key="cellIndex">
          {{cell}}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';

export default {
  name: 'Sudoku',
  data() {
    return {
      size: 9,
      gameValues: [],
      level: 0, // TODO: add level select
    }
  },
  mounted () {
    const sudokusFile = require(`raw-loader!./../assets/${this.level}.txt`);
    const sudokus = sudokusFile.default.split('\n');
    const number = Math.floor(Math.random() * (sudokus.length + 1));
    const sudokuString = sudokus[number].trim();
    this.size = Math.sqrt(sudokuString.length);

    for (let i = 0; i < this.size; i++) {
      const startingIndex = i * this.size;
      const values = sudokuString.slice(startingIndex, startingIndex + this.size);
      const gameValues = [];

      for (let j = 0; j < values.length; j++) {
        gameValues.push(values[j] !== '0' ? parseInt(values[j]) : null);
      }
      
      Vue.set(this.gameValues, i, gameValues);
    }
  },
}
</script>

<style scoped lang="scss">
.game-container {
  display: flex;
  justify-content: center;

  .game-board {
    border: 2px solid black;

    .game-row {
      display: flex;
      
      &:nth-child(3n) {
        border-bottom: 2px solid black;
      }

      .game-cell {
        width: 40px;
        height: 40px;
        border: 1px solid black;
        justify-content: center;
        align-items: center;
        display: flex;

        &:nth-child(3n):not(:last-child) {
          border-right: 2px solid black;
        }
      }
    }
  }
}
</style>
