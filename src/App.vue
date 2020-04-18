<template>
  <div id="app">
    <NoteManager title="Note Manager"/>
  </div>
</template>

<script>
import NoteManager from './components/NoteManager.vue'

export default {
  name: 'App',
  components: {
    NoteManager
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin-left: 10vw;
  margin-right: 10vw;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
