<template>
  <div id="app">
    <SearchWidget csvLink="https://raw.githubusercontent.com/dms-vep/project-homepage/refs/heads/main/results/summaries/summary_of_all_phenotypes.csv?token=GHSAT0AAAAAACW7UDYRY4PASHKZ3G7YLAVEZXS5SAA" mutationType="site,wildtype,mutant"/>
  </div>
</template>

<script>
// import DMSWidget from './components/DMSWidget.vue'
import SearchWidget from './components/searchWidget.vue';

export default {
  name: 'App',
  components: {
    SearchWidget
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
