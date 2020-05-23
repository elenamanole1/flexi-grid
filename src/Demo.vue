<template>
  <div id="app">
    <h1>Flexi Grid</h1>
    <FlexiGrid title="Flexi Grid Saves The World" msg="Here Goes our complicated code" />
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import FlexiGrid from "./components/FlexiGrid.vue";

@Component({
  components: {
    FlexiGrid
  }
})
export default class Demo extends Vue {}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
