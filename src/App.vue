<template>
  <div id="app">
    <FlowEditor/>
    <!--
    <FlowEditor v-bind:components="[{type: 'box', x: 10, y: 15, label: 'Hiii!' }]" />
    -->
  </div>
</template>

<script>
// import HelloWorld from "./components/HelloWorld";
import FlowEditor from "./components/FlowEditor";

export default {
  name: "App",
  components: {
    FlowEditor
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
