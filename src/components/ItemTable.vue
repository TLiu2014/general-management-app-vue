<template>

  <div>
    <b-table striped hover :fields="fields" :items="items">
      <template slot="index" slot-scope="data">
        {{data.index + 1}}
      </template>
      <template slot="id" slot-scope="data">
        {{data.item.itemId}}
      </template>
      <template slot="name" slot-scope="data">
        {{(isEdit!==data.item.itemId)? data.item.name : null}}
        <b-form-group id="editNameInputGroup" v-if="isEdit===data.item.itemId">
          <b-form-input id="editNameInput"
                        type="text"
                        v-model="editForm.name"
                        required
                        placeholder="Enter an item name">
          </b-form-input>
        </b-form-group>
      </template>
      <template slot="value" slot-scope="data">
        {{(isEdit!==data.item.itemId)? data.item.value : null}}
        <b-form-group id="editValueInputGroup" v-if="isEdit===data.item.itemId">
          <b-form-input id="editValueInput"
                        type="text"
                        v-model="editForm.value"
                        required
                        placeholder="Enter a value">
          </b-form-input>
        </b-form-group>
      </template>
      <template slot="edit" slot-scope="data">
        <b-btn v-if="isEdit===data.item.itemId" size="md" variant="primary" @click="onSubmitEdit(data.item.itemId)">Submit</b-btn>
        <b-btn v-if="isEdit===data.item.itemId" size="md" variant="secondary" @click="onEditMode(null)">Cancel</b-btn>
        <b-btn v-else size="md" variant="primary" @click="onEditMode(data.item.itemId)">Edit</b-btn>
      </template>
      <template slot="delete" slot-scope="data">
        <b-btn size="md" variant="danger" @click="deleteItem(data.item.itemId)">Delete</b-btn>
      </template>
    </b-table>

    <b-form @submit="onSubmit" @reset="onReset" v-if="show">
      <b-form-group id="nameInputGroup"
                    label="Name:"
                    label-for="nameInput">
        <b-form-input id="nameInput"
                      type="text"
                      v-model="form.name"
                      required
                      placeholder="Enter an item name">
        </b-form-input>
      </b-form-group>
      <b-form-group id="valueInputGroup"
                    label="Value:"
                    label-for="valueInput">
        <b-form-input id="valueInput"
                      type="text"
                      v-model="form.value"
                      required
                      placeholder="Enter a value">
        </b-form-input>
      </b-form-group>
      <b-button type="submit" variant="primary">Submit</b-button>
      <b-button type="reset" variant="danger">Reset</b-button>
    </b-form>
  </div>
</template>

<script>
import axios from "axios";
import { Environment } from "../config.js";
var qs = require('qs');
const items = [];
const fields = [
  'index',
  { key: 'id', label: 'ID' },
  { key: 'name', label: 'Name' },
  { key: 'value', label: 'Value' },
  'edit',
  'delete'
];

export default {
  name: "ItemTable",
  data () {
    return {
      fields: fields,
      items: items,
      form: {
        name: '',
        value: '',
      },
      editForm: {
        name: '',
        value: '',
      },
      show: true,
      isEdit: null,
      errors: []
    }
  }, 
  async mounted () {
    try {
      const response = await axios.get(Environment.API_URL + "/items");
      console.log('GET all items response', response.data);
      this.items = response.data.sort((a, b) => a.itemId < b.itemId ? -1: 1);
    } catch (e) {
      this.errors.push(e);
    }
  },
  methods: {
    onEditMode(itemId) {
      this.isEdit = itemId;
      const editItem = this.items.find(e => e.itemId === itemId);
      if (itemId) {
        this.editForm = {
          name: editItem.name,
          value: editItem.value,
        };
        delete this.editForm.itemId;
      } else {
        this.editForm = null;
      }
    },
    async onSubmitEdit(itemId) {
      try {
        const response = await axios.put(Environment.API_URL + "/items/" + itemId, this.editForm);
        console.log('PUT response', response.data);
        this.items = this.items.filter(e => e.itemId !== itemId);
        this.isEdit = null;
        // push & sort can't be put in one line..
        this.items.push(response.data);
        this.items.sort((a, b) => a.itemId < b.itemId ? -1: 1);
      } catch (e) {
        this.errors.push(e);
      }
    },
    async onSubmit (evt) {
      evt.preventDefault();
      try {
        const response = await axios.post(Environment.API_URL + "/items", qs.stringify(this.form));
        this.items.push(response.data);
      } catch (e) {
        this.errors.push(e);
      }
    },
    onReset (evt) {
      evt.preventDefault();
      this.form.name = '';
      this.form.value = '';
      
      this.show = false;
      this.$nextTick(() => { this.show = true });
    },
    async deleteItem(evt) {
      const itemId = evt;
      this.items = this.items.filter(e => e.itemId !== itemId);
      try {
        const response = await axios.delete(Environment.API_URL + "/items/" + itemId);
      } catch (e) {
        this.errors.push(e);
      }
    },

    // details(item) {
    //   alert(JSON.stringify(item));
    // },
  }
};
</script>
