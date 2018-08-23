<template>
  <div id="app">
    <h1>The Simple Cron Resolver</h1>
    <CronResolver/>
    <footer>designed and coded by 2EN0NE</footer>
  </div>
</template>

<script>
import CronResolver from './components/CronResolver.vue'

export default {
  name: 'app',
  components: {
    CronResolver
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
  margin-top: 60px;
}
</style>
