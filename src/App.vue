<template>
  <div id="app">
    <!-- TODO: add condition when to show game -->
    <TicTacToe v-if="false"/>
    <Sudoku/>
  </div>
</template>

<script>
import TicTacToe from './components/TicTacToe.vue'
import Sudoku from './components/Sudoku.vue'

export default {
  name: 'App',
  components: {
    TicTacToe,
    Sudoku,
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
