<template>
  <div id="app">
    <ItemTable />
  </div>
</template>

<script>
import ItemTable from "./components/ItemTable.vue";

export default {
  name: "app",
  components: {
    ItemTable
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
