<template>
  <div id="app">

    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
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
  background: url(/src/assets/desktop.jpg) no-repeat center center fixed;
}
body {
  height: px;
  min-height: 100%;

  background-size: cover;
  color: antiquewhite;
  font-family: 'Roboto Condensed', sans-serif;
}

</style>
