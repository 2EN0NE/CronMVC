<template>
  <div id="app">
    <h1>The Simple Cron Resolver</h1>
    <CronResolver/>
    <footer class="footer">Designed and Coded by 2EN0NE</footer>
  </div>
</template>

<script>
import CronResolver from "./components/CronResolver.vue";

export default {
  name: "app",
  components: {
    CronResolver
  }
};
</script>

<style lang="scss">
@import "../../../public/variables.scss";

*,
*::before,
*::after {
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: $vue-blue;
  margin-top: 60px;
}
.footer {
  margin: 2ch;
}
.row {
  position: relative;
  margin-left: 0;
  margin-right: 0;
  height: auto;
  zoom: 1;
  display: block;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
.row:after {
  clear: both;
}
.row:before,
.row:after {
  content: "";
  display: table;
}
.col-1,
.col-2,
.col-3,
.col-4,
.col-5,
.col-6,
.col-7,
.col-8,
.col-9,
.col-10,
.col-11,
.col-12 {
  float: left;
  -webkit-box-flex: 0;
  -webkit-flex: 0 0 auto;
  -ms-flex: 0 0 auto;
  flex: 0 0 auto;
}
.col-3 {
  display: block;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  width: 25%;
}
.col-4 {
  display: block;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  width: 33.33333333%;
}
.col-8 {
  display: block;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  width: 66.66666667%;
}
.col-9 {
  display: block;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
  width: 75%;
}
</style>
