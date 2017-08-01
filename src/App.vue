<template>
  <div id="app">
    <home></home>
  </div>
</template>

<script>
// import Hello from './components/Hello'
import Home from './components/Home'

export default {
  name: 'app',
  components: {
    Home
  }
}
</script>

<style>
/* public style & fix */
body {
  margin: 0 !important;
}

* {
  box-sizing: border-box;
}

a, a:hover, a:active, a:visited, a:link {
  text-decoration: none;
  color: #222;
}

ul, li {
  list-style: none;
}

p {
  margin: 0;
}

#app {
  font-family: 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', 'Helvetica Neue', Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #222;
  min-width: 1170px;
}
</style>
