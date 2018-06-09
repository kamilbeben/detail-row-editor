<template>
  <div id="app">
    <my-vuetable/>
  </div>
</template>

<script>
import Vue from 'vue'
import MyVuetable from './components/MyVuetable'

export default {
  name: 'App',
  components: {
    MyVuetable
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
