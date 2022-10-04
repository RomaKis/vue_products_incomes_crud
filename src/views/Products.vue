<template>
  <main>
    <div class="product-page">
      <h2>Products:</h2>
      <div>
        <span>Photo</span>
        <span>Name</span>
        <span>Type</span>
        <span>Guarantee</span>
      </div>
      <ul class="products">
        <li
            v-for="product in products"
            :key="product.title"
            class="product"
        >
          <img :src="product.photo">
          <span>{{ product.title }}</span>
          <span>{{ product.type }}</span>
          <span><span v-if="product.guarantee">{{ product.guarantee.start }} - {{ product.guarantee.end }}</span></span>
          <button class="btn btn-danger" @click="deleteProduct(product.id)">Delete</button>
        </li>
      </ul>

      <button class="add-new btn btn-primary" @click="showModal = true">Add new Product</button>
    </div>

    <div id="modal" class="modal-custom" v-show="showModal === true">
      <div class="modal-content">
        <span class="close" @click="showModal = false">&times;</span>
        <p v-if="errors.length">
          <b>Please correct the following error(s):</b>
        <ul>
          <li v-for="error in errors">{{ error }}</li>
        </ul>
        </p>


        <div class="form-group">
          <label for="product_name">Product Name</label>
          <input id="product_name" class="form-control" v-model="name"/>
        </div>
        <div class="form-group">
          <label for="product_type">Product Type</label>
          <input id="product_type" class="form-control" v-model="type"/>
          <button class="submit-add-new btn btn-primary" @click="createNewProduct()">Create</button>
        </div>
      </div>
    </div>
  </main>
</template>
<script>
import axios from "axios";

//need to add is some way to .env
const baseUrl = 'http://localhost:3000/products';

export default {
  data() {
    return {
      type: '',
      name: '',
      products: [],
      showModal: false,
      errors: [],
    }
  },
  methods: {
    getProducts: async function () {
      try {
        await axios.get(baseUrl).then(resp => {
          this.products = resp.data;
        });
      } catch (error) {
      }
    },
    createNewProduct: async function () {
      this.errors = [];

      if (!this.name) {
        this.errors.push('Name required.');
      }

      if (!this.type) {
        this.errors.push('Type required.');
      }

      if (this.errors.length) {
        return;
      }
      this.showModal = false;

      try {
        await axios.post(baseUrl, {
          title: this.name,
          type: this.type,
          photo: './src/assets/images/laptop.jpeg'
        }).then(resp => {
          this.getProducts();
        });
      } catch (error) {
      }
    },
    deleteProduct: async function (id) {
      try {

        await axios.delete(baseUrl + '/' + id).then(resp => {
          this.getProducts();
        });
      } catch (error) {
      }
    }
  },
  async created() {
    this.getProducts();
  },
}
</script>
