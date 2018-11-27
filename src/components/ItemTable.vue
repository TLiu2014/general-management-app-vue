<template>
  <b-table striped hover :fields="fields" :items="items">
    <template slot="index" slot-scope="data">
      {{data.index + 1}}
    </template>
    <template slot="id" slot-scope="data">
      {{data.item.itemId}}
    </template>
    <template slot="name" slot-scope="data">
      {{data.item.name}}
    </template>
    <template slot="value" slot-scope="data">
      {{data.item.value}}
    </template>
  </b-table>
</template>

<script>
import axios from "axios";
import { Environment } from "../config.js";
const items = [];
const fields = [
  'index',
  { key: 'id', label: 'ID' },
  { key: 'name', label: 'Name' },
  { key: 'value', label: 'Value' }
];

export default {
  name: "ItemTable",
  data () {
    return {
      fields: fields,
      items: items,
    }
  }, 
  mounted () {
    axios.get(Environment.API_URL + "/items")
    .then(response => {
      console.log(response);
      this.items = response.data
    });
  }
};
</script>
