<template>
  <div id="app">
    <Pic />
  </div>
</template>

<script>
import Pic from "./views/Pic";

export default {
  name: "app",
  components: {
    Pic,
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
* {
  margin: 0;
  padding: 0;
}
</style>
