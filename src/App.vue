<template>
  <div id="app" class="w-100 p-3">
    <!-- <img alt="Vue logo" src="./assets/logo.png"> -->
    <h1>Upload your image file!</h1>
    <UserFileUpload msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import UserFileUpload from './components/UserFileUpload.vue'

export default {
  name: 'App',
  components: {
    UserFileUpload
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
