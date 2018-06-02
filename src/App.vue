<template>
  <div id="app">
    <Life :start-width="20" :start-height="20"/>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App2"/> -->
  </div>
</template>

<script>
import HelloWorld from "./components/HelloWorld.vue";
import Life from "./components/Life.vue";

export default {
  name: "app",
  components: {
    HelloWorld,
    Life
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
