<template>
  <div id="app">
    <books-list/>
  </div>
</template>

<script>
import BooksList from './components/BooksList.vue'

export default {
  name: 'App',
  components: {
    BooksList
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
}
</style>
