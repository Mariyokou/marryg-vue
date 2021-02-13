<template>
  <div class="game-container">
    <div class="game-board">
      <div class="game-row" 
        v-for="(row, rowIndex) in gameValues" 
        :key="rowIndex">
        <div class="game-cell" 
          v-for="(cell, cellIndex) in gameValues[rowIndex]" 
          :key="cellIndex">
          <button>
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
    }
  },
  created() {
    for(let i = 0; i < this.size; i++) {
      Vue.set(this.gameValues, i, Array(this.size).fill(null));
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
        }
      }
    }
  }
}
</style>
