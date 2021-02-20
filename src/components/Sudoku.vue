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
            :class="{'has-error': repeatingValuesByIndex[rowIndex].includes(cell)}"
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
      repeatingValuesByIndex: {},
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
      Vue.set(this.repeatingValuesByIndex, i, []);
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

      //TODO: add button for validation enabling/disabling
      this.setRepeatingValues();
      this.checkValuesHorizontally();
      this.checkValuesVertically();
      this.checkValuesRegionally();
    },
    setRepeatingValues() {
      this.repeatingValuesByIndex = {};
      this.gameValues.forEach((row, rowIndex) => {
        this.repeatingValuesByIndex[rowIndex] = [];
      });
    },
    getRepeatingValues(list) {
      return list.filter((item, index) => 
        item && list.indexOf(item) !== index
      );
    },
    getRepeatingValueIndexes(list) {
      const repeatingValues = this.getRepeatingValues(list);
      
      return list.reduce((a, e, i) => 
        (repeatingValues.includes(e)) 
          ? a.concat(i) 
          : a
      , []);
    },
    checkValuesHorizontally() {
      this.gameValues.forEach((row, rowIndex) => {
        const repeatingValues = this.getRepeatingValues(row);
        this.repeatingValuesByIndex[rowIndex] = repeatingValues.concat(this.repeatingValuesByIndex[rowIndex]);
      });
    },
    checkValuesVertically() {
      this.gameValues.forEach((row, rowIndex) => {
        const columnValues = [];
        
        row.forEach((cell, cellIndex) => {
          columnValues.push(this.gameValues[cellIndex][rowIndex]);
        });

        const repeatingValueIndexes = this.getRepeatingValueIndexes(columnValues);

        repeatingValueIndexes.forEach((index) => {
          this.repeatingValuesByIndex[index].push(columnValues[index]);
        });
      });
    },
    checkValuesRegionally() {
      const regionSize = Math.sqrt(this.size);
      
      for (let rowIndex = 0; rowIndex < this.size; rowIndex += regionSize) {
        const rows = this.gameValues.slice(rowIndex, rowIndex + regionSize);

        for (let cellIndex = 0; cellIndex < this.size; cellIndex += regionSize) {
          const regions = [];

          for (let rowIndexInRegion = 0; rowIndexInRegion < regionSize; rowIndexInRegion++) {
            regions.push(...rows[rowIndexInRegion].slice(cellIndex, cellIndex + regionSize));
          }

          const repeatingValueIndexes = this.getRepeatingValueIndexes(regions);

          repeatingValueIndexes.forEach((index) => {
            const repeatingRowIndex = Math.floor(index / regionSize) + rowIndex;
            this.repeatingValuesByIndex[repeatingRowIndex].push(regions[index]);
          });
        }
      }
    },
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
      justify-content: center;
      
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

          &.has-error {
            color: red;
          }

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
