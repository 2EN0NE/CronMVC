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
  display: block;
  -webkit-box-sizing: border-box;
  box-sizing: border-box;
}
.col-1 {
  width: 8.33333333%;
}
.col-2 {
  width: 16.66666667%
}
.col-3 {
  width: 25%;
}
.col-4 {
  width: 33.33333333%;
}
.col-5 {
  width: 41.666666667;
}
.col-6 {
  width: 50%;
}
.col-7 {
  width: 58.33333333%;
}
.col-8 {
  width: 66.66666667%;
}
.col-9 {
  width: 75%;
}
.col-10 {
  width: 83.33333333%;
}
.col-11 {
  width: 91.66666667%;
}
.col-12 {
  width: 100%;
}
</style>
