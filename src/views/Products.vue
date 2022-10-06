<template>
  <main>
    <div class="product-page">
      <div>
        <button class="add-new" @click="showModal = true">+</button>
        <h2>Products</h2>
      </div>
      <table>
        <tr>
          <th>Photo</th>
          <th>Name</th>
          <th>Type</th>
          <th>Guarantee</th>
          <th></th>
        </tr>
        <tr class="products" v-for="product in products" :key="product.title">
          <td> <img :src="product.photo" class="img-products-list"></td>
          <td>{{ product.title }}</td>
          <td>{{ product.type }}</td>
          <td><span v-if="product.guarantee">{{ product.guarantee.start }} - {{ product.guarantee.end
          }}</span></td>
          <td><button class="btn-delete-product" @click="deleteProduct(product.id)"><img
                src="../assets/images/trash.svg"></button></td>
        </tr>
      </table>



    </div>

    <div id="modal" class="modal-custom" v-show="showModal === true">
      <div class="modal-content">
        <span class="close" @click="showModal = false">&times;</span>
        <p class="error-list" v-if="errors.length">
          <b class="error-header">Please correct the following error(s):</b>
        <ul>
          <li class="error-option" v-for="error in errors">{{ error }}</li>
        </ul>
        </p>

        <div class="form-group">
          <label for="product_name">Product Name</label>
          <input id="product_name" class="form-control" v-model="name" />
        </div>
        <div class="form-group">
          <label for="product_type">Product Type</label>
          <input id="product_type" class="form-control" v-model="type" />

        </div>
        <div class="btn-container">
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
