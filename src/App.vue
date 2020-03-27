<template>
  <div id="app">
    <NavBar/>
    <div class="container">
       <Home/>
    </div>
  </div>
</template>

<script>
import NavBar from './components/NavBar.vue'
import Home from './components/Home.vue'
export default {
  name: 'App',
  components: {
    NavBar,
    Home
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 70px;
  margin-bottom: 60px;
}
</style>
