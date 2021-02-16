<template>
  <div class="game-container">
    <div class="game-board">
      <div class="game-row" 
        v-for="(row, rowIndex) in gameValues" 
        :key="rowIndex">
        <div class="game-cell" 
          v-for="(cell, cellIndex) in gameValues[rowIndex]" 
          :key="cellIndex">
          <span v-if="initialGameValues[rowIndex][cellIndex]">{{cell}}</span>
          <input v-else type="number" min=1 :max="size"
            @input="setAndCheckValue(rowIndex, cellIndex, $event)">
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
      initialGameValues: [],
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
      
      Vue.set(this.gameValues, i, gameValues.slice());
      Vue.set(this.initialGameValues, i, gameValues.slice());
    }
  },
  methods: {
    setAndCheckValue(rowIndex, columnIndex, event) {
      const enteredValue = Number(event.target.value);
      if (enteredValue < 1 || enteredValue > this.size) {
        console.log('Bad value') //TODO: message dispaly
        return;
      }

      Vue.set(this.gameValues[rowIndex], columnIndex, enteredValue);

      //TODO: values validation
    }
  }
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

        span {
          background-color: #d4d4d4;
          width: 100%;
          height: 100%;
          align-items: center;
          justify-content: center;
          display: flex;
        }

        input {
          width: 90%;
          height: 90%;
          border: none;
          text-align: center;
          -moz-appearance: textfield;

          &:focus {
            outline: none;
          }

          &::-webkit-outer-spin-button,
          &::-webkit-inner-spin-button {
            -webkit-appearance: none;
          }
        } 

        span, input {
          font-size: 16px;
        }       
      }
    }
  }
}
</style>
