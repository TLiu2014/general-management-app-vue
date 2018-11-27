<template>
  <div id="app">
    <b-row>
        <b-col sm="10" offset-sm="1">
          <ItemTable />
        </b-col>
    </b-row>
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
}
</style>
