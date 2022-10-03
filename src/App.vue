<template>
  <div id="app">
    <Board></Board>
  </div>
</template>

<script>
import Board from './components/Board.vue'
import Peg from './components/Peg.vue'

export default {
  name: 'App',
  components: {
    Board,
    Peg
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100vh;
  width: 100vw;
  margin: 0;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
}
</style>
