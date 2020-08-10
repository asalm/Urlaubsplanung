<template>
  <div id="app">
    <div class="container">
    <Formular/>
    </div>
    
  </div>
</template>

<script>
import Formular from './components/Formular.vue'

export default {
  name: 'App',
  components: {
    Formular
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
