<template>
  <div id="app">
    <DMSWidget />
  </div>
</template>

<script>
import DMSWidget from './components/DMSWidget.vue'

export default {
  name: 'App',
  components: {
    DMSWidget
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
