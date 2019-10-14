<template lang="pug">

    .app__container
      section.app-logo
        img(alt="logo" src="./assets/img/logo.jpg")
      Cadastro

</template>

<script>
import Cadastro from './components/Cadastro.vue'

export default {
  name: 'app',
  components: {
    Cadastro
  }
}
</script>

<style>
  body {
    background-color: black;
    color: #fff;
  }
  #app {
    font-family: Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #fff;
    margin-top: 60px;
  }

  .app__container {
    width: 630px;
    margin: 0 auto;
  }

  .app-logo > img {
    width: 100%;
  }
</style>
