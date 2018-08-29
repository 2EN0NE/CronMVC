<template>
  <div id="app">
    <h1>The Simple Cron Resolver</h1>
    <CronResolver/>
    <footer class="footer">Designed and Coded by 2EN0NE</footer>
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

<style lang="scss" scoped>
@import '../../../public/variables.scss';

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: $vue-blue;
  margin-top: 60px;
}
.footer {
  margin: 2ch
}
</style>
