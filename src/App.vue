<template>
  <div id="app">
    <RequestBox/>
  </div>
</template>

<script>
import RequestBox from './components/RequestBox.vue'

export default {
  name: 'App',
  components: {
    RequestBox
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  margin-top: 30px;
}
</style>
