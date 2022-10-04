<script setup lang="ts">

</script>

<template>
  <main>
    <h2>Incomes({{ incomesQty }}):</h2>
    <div class="incomes-page">
      <div class="incomes">
        <ul class="incomes-list">
          <li @click="showProductsForIncome(income.id)"
              v-for="income in incomes"
              :key="income.title"
              class="income"
          >
            <span>{{ income.title }}</span>
            <span v-if="income.products">
            <span v-if="income.products.length === 1">{{ income.products.length }} product</span>
            <span v-if="income.products.length > 1">{{ income.products.length }} products</span>
          </span>
            <span>{{ income.date }}</span>
            <span>{{ income.description }}</span>
            <button class="btn btn-danger" @click="deleteIncome(income.id)">Delete Income</button>
          </li>
        </ul>

        <button class="add-new btn btn-primary" @click="showModalToAddIncome = true">Add new Income</button>
      </div>
      <div class="income-products">
        <button class="btn btn-success" v-show="incomeChosenId !== false" @click="showModalAddProductToIncome =
       true">Add Product To Income
        </button>
        <ul class="products">
          <li
              v-for="product in productsForIncome"
              :key="product.title"
              class="product"
          >
            <img :src="product.photo">
            <span>{{ product.title }}</span>
            <span>{{ product.type }}</span>
            <button class="btn btn-danger" @click="deleteProductFromIncome(product.id)">Delete Product From Income
            </button>
          </li>
        </ul>
      </div>
    </div>

    <div id="modal-add-product-to-income" class="modal-custom" v-show="showModalAddProductToIncome === true">
      <div class="modal-content">
        <span class="close" @click="showModal = false">&times;</span>
        <select v-model="selectedAddProductToIncome">
          <option value="">Please Select</option>
          <option v-for="option in products" :value="option.id">{{ option.title }}</option>
        </select>
        <button class="btn btn-success" @click="addProductToIncome()">Add</button>
      </div>
    </div>

    <div id="modal-add-new-income" class="modal-custom" v-show="showModalToAddIncome === true">
      <div class="modal-content">
        <span class="close" @click="showModalToAddIncome = false">&times;</span>
        <p v-if="errors.length">
          <b>Please correct the following error(s):</b>
        <ul>
          <li v-for="error in errors">{{ error }}</li>
        </ul>
        </p>

        <div class="form-group">
          <label for="income_description">Income Description</label>
          <input id="income_description" class="form-control" v-model="description"/>
        </div>
        <div class="form-group">
          <label for="income_title">Income Title</label>
          <input id="income_title" class="form-control" v-model="title"/>
        </div>
        <div class="form-group">
          <label for="income_date">Income Date</label>
          <input id="income_date" class="form-control" v-model="date"/>
        </div>
        <button class="submit-add-new btn btn-primary" @click="createNewIncome()">Create</button>
      </div>
    </div>
  </main>
</template>
<script>
import axios from "axios";

//need to add is some way to .env
const baseUrlIncomes = 'http://localhost:3000/incomes';
const baseUrlProducts = 'http://localhost:3000/products';

export default {
  data() {
    return {
      description: '',
      title: '',
      date: '',
      incomes: [],
      showModalAddProductToIncome: false,
      showModalToAddIncome: false,
      errors: [],
      incomesQty: 0,
      productsForIncome: [],
      products: [],
      incomeChosenId: false,
      selectedAddProductToIncome: ''
    }
  },
  methods: {
    getIncomes: async function () {
      try {
        await axios.get(baseUrlIncomes).then(resp => {
          this.incomes = resp.data;
          this.incomesQty = this.incomes.length;
        });
      } catch (error) {
      }
    },

    showProductsForIncome: function (incomeId) {
      let income = this.incomes.find(x => x.id === incomeId),
          productIds = income.products,
          self = this;

      this.incomeChosenId = incomeId;
      self.productsForIncome = [];
      axios.get(baseUrlProducts).then(resp => {
        self.products = resp.data;
        resp.data.forEach(function (item, i) {
          if (productIds.includes(item.id)) {
            self.productsForIncome.push(item);
          }
        });
      })
    },

    createNewIncome: async function () {
      this.errors = [];

      if (!this.title) {
        this.errors.push('Title required.');
      }

      if (!this.description) {
        this.errors.push('Description required.');
      }

      if (!this.date) {
        this.errors.push('Date required.');
      }

      if (this.errors.length) {
        return;
      }
      this.showModalToAddIncome = false;

      try {
        await axios.post(baseUrlIncomes, {
          title: this.title,
          description: this.description,
          date: this.date,
        }).then(resp => {
          this.getIncomes();
        });
      } catch (error) {
      }
    },
    deleteProductFromIncome: async function (productId) {
      let income = this.incomes.find(x => x.id === this.incomeChosenId),
          productsInIncome = JSON.parse(JSON.stringify(income.products));

      const index = productsInIncome.indexOf(productId);
      if (index > -1) {
        productsInIncome.splice(index, 1);
        income.products = productsInIncome;
        try {
          await axios.put(baseUrlIncomes + '/' + this.incomeChosenId, income).then(resp => {
          });
        } catch (error) {
        }
      }
      this.showProductsForIncome(income.id);
    },
    addProductToIncome: async function (productId) {
      let income = this.incomes.find(x => x.id === this.incomeChosenId),
          productsInIncome = [],
          productToAdd = this.selectedAddProductToIncome;

      if (income.products) {
        productsInIncome = JSON.parse(JSON.stringify(income.products))
      }

      if (productToAdd && !productsInIncome.includes(productToAdd)) {
        productsInIncome.push(productToAdd);
        income.products = productsInIncome;

        try {
          await axios.put(baseUrlIncomes + '/' + this.incomeChosenId, income).then(resp => {
          });
        } catch (error) {
        }
      }
      this.showModalAddProductToIncome = false;
      this.showProductsForIncome(income.id);
    },
    deleteIncome: async function (id) {
      try {
        self = this;
        await axios.delete(baseUrlIncomes + '/' + id).then(resp => {
          this.getIncomes();
          self.incomeChosenId = false;
          self.productsForIncome = [];
        });
      } catch (error) {
      }
    }
  },
  async created() {
    this.getIncomes();
  },
}
</script>
