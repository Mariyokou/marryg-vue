<template>
  <div class="game-container">
    <div class="game-board">
      <div class="game-row" 
        v-for="(row, rowIndex) in gameValues" 
        :key="rowIndex">
        <div class="game-cell" 
          v-for="(cell, cellIndex) in gameValues[rowIndex]" 
          :key="cellIndex">
          <button 
            :class="{'filled': gameValues[rowIndex][cellIndex]}" 
            @click="setAndCheckValue(rowIndex, cellIndex)">
            {{cell}}
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Vue from 'vue';

export default {
  name: 'TicTacToe',
  data() {
    return {
      size: 3,
      gameValues: [],
      currentPlayer: '1',
      symbolsByPlayer: {
        1: 'x',
        2: 'o',
      },
    }
  },
  created() {
    for(let i = 0; i < this.size; i++) {
      Vue.set(this.gameValues, i, Array(this.size).fill(null));
    }
  },
  methods: {
    setAndCheckValue(rowIndex, columnIndex) {
      if (this.gameValues[rowIndex][columnIndex] || this.isWin) {
        return;
      }

      Vue.set(
        this.gameValues[rowIndex], 
        columnIndex, 
        this.symbolsByPlayer[this.currentPlayer]
      );

      this.isWin = this.checkWinnerHorizontally() 
        || this.checkWinnerVertically()
        || this.checkWinnerDiagonally();
      
      this.currentPlayer = this.currentPlayer === '1'
        ? '2' 
        : '1';      
    },
    checkWinnerHorizontally() {
      let isFilled = false;

      this.gameValues.forEach((row) => {
        if (isFilled) { return; }

        const uniqSymbolsInLine = [...new Set(row)];
        isFilled = uniqSymbolsInLine.length === 1 && !!uniqSymbolsInLine[0];
      });

      return isFilled;
    },
    checkWinnerVertically() {
      let isFilled = false;

      this.gameValues.forEach((row, index) => {
        if (isFilled) { return; }

        const columnValues = [];

        row.forEach((cell, index2) => {
          const item = this.gameValues[index2][index];

          if (!columnValues.includes(item)) {
            columnValues.push(item);
          }
        });

        isFilled = columnValues.length === 1 && !!columnValues[0];
      });

      return isFilled;
    },
    checkWinnerDiagonally() {
      const diagonal1Values = [];
      const diagonal2Values = [];

      this.gameValues.forEach((row, index) => {
        const item = this.gameValues[index][index];
        const item2 = this.gameValues[index][this.size - 1 - index];

        if (!diagonal1Values.includes(item)) {
          diagonal1Values.push(item);
        }
        if (!diagonal2Values.includes(item2)) {
          diagonal2Values.push(item2);
        }
      });

      return (diagonal1Values.length === 1 && !!diagonal1Values[0])
        || (diagonal2Values.length === 1 && !!diagonal2Values[0]);
    }
  }
}
</script>

<style scoped lang="scss">
.game-container {
  display: flex;
  justify-content: center;

  .game-board {
    display: flex;
    flex-direction: column;

    .game-row {
      display: flex;
      justify-content: center;

      &:not(:last-of-type) {
        border-bottom: 3px solid black;
      }

      .game-cell {
        width: 90px;
        height: 90px;

        &:not(:last-child) {
          border-right: 3px solid black;
        }

        button {
          width: 100%;
          height: 100%;
          border: none;
          background: white;
          font-size: 70px;
          display: inline-flex;
          justify-content: center;

          &:focus{
            outline: none;
          }

          &:not(.filled):hover {
            background: #dcdcdc;
            cursor: pointer;
          }
        }
      }
    }
  }
}
</style>
