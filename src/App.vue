<template>
  <div id="app" class="container">
    <Main/>
  </div>
</template>

<script>
    import Main from './components/Main.vue'

    export default {
        name: 'app',
        components: {
            Main
        }
    }
</script>

<style>

  body, html {
    margin: 0;
    height: 100%;
  }
  * {
    box-sizing: border-box;
  }
  .container {
    max-width: 600px;
    margin: 0 auto;
  }
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    color: #2c3e50;
    margin-top: 60px;
    font-size: 24px;
  }
</style>
