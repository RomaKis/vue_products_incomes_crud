<template>
  <main>
    <div class="incomes-count">
      <button class="add-new btn-add-new-income" @click="showModalToAddIncome = true">+</button>
      <h2>Incomes / {{ incomesQty }}</h2>
    </div>
    <div class="incomes-page">
      <div class="incomes">
        <ul class="incomes-list">
          <li @click="showProductsInIncome(income.id)" v-for="income in incomes" :key="income.title"
              v-bind:class="{ active: income.id === incomeChosen.id }">
            <span>{{ income.title }}</span>
            <span>
            <span v-if="income.products">
              <span v-if="income.products.length === 1">{{ income.products.length }} product</span>
              <span v-if="income.products.length > 1">{{ income.products.length }} products</span>
            </span>
          </span>
            <span class="new-income-date">{{ income.date }}</span>
            <span>{{ income.description }}</span>
            <button class="btn btn-delete-income" @click="deleteIncome(income.id)"><img src="../assets/images/trash.svg"
                                                                                        alt=""></button>
          </li>
        </ul>

      </div>
      <div class="income-products" v-show="incomeChosen !== false">
        <div class="close-products-in-income">
            <button class="btn-close-products-in-income" @click="closeProductsInIncome()"> X </button>
          </div>
        <div class="products-top-line">
          <div class="income-description-products">{{ incomeChosen.description }}</div>
          <div class="div-for-btn-add-product">
            <button class="btn-add-product-to-income" @click="showModalAddProductToIncome = true"> +
              <span>Add product</span>
            </button>
          </div>
         
        </div>
       
        <ul class="products">
          <li v-for="product in productsForIncome" :key="product.title" class="product">
            <img :src="product.photo" class="img-product">
            <span>{{ product.title }}</span>
            <span>{{ product.type }}</span>
            <button class="btn-delete-product-from-income" @click="deleteProductFromIncome(product.id)"><img
                src="../assets/images/trash.svg"
                alt=""></button>
          </li>
        </ul>
      </div>
    </div>

    <div id="modal-add-product-to-income" class="modal-custom" v-show="showModalAddProductToIncome === true">
      <div class="modal-content">
        <span class="close" @click="showModalAddProductToIncome = false">&times;</span>
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
        <p class="error-list" v-if="errors.length">
          <b class="error-header">Please correct the following error(s):</b>
        <ul>
          <li class="error-option" v-for="error in errors">{{ error }}</li>
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
      incomeChosen: false,
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

    showProductsInIncome: function (incomeId) {
      let income = this.incomes.find(x => x.id === incomeId),
      productIds = JSON.parse(JSON.stringify(income.products)),
          self = this;

      this.incomeChosen = income;
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
      let income = this.incomes.find(x => x.id === this.incomeChosen.id),
          productsInIncome = JSON.parse(JSON.stringify(income.products));

      const index = productsInIncome.indexOf(productId);
      if (index > -1) {
        productsInIncome.splice(index, 1);
        income.products = productsInIncome;
        try {
          await axios.put(baseUrlIncomes + '/' + this.incomeChosen.id, income).then(resp => {
          });
        } catch (error) {
        }
      }
      this.showProductsInIncome(income.id);
    },
    addProductToIncome: async function (productId) {
      let productsInIncome = [],
          productToAdd = this.selectedAddProductToIncome;

      if (this.incomeChosen.products) {
        productsInIncome = JSON.parse(JSON.stringify(this.incomeChosen.products))
      }

      if (productToAdd && !productsInIncome.includes(productToAdd)) {
        productsInIncome.push(productToAdd);
        this.incomeChosen.products = productsInIncome;

        try {
          await axios.put(baseUrlIncomes + '/' + this.this.incomeChosen.id, this.incomeChosen).then(resp => {
          });
        } catch (error) {
        }
      }
      this.showModalAddProductToIncome = false;
      this.showProductsInIncome(this.incomeChosen.id);
    },
    closeProductsInIncome: function () {
      this.incomeChosen = false
      this.productsForIncome = [];
    },
    deleteIncome: async function (id) {
      try {
        self = this;
        await axios.delete(baseUrlIncomes + '/' + id).then(resp => {
          this.getIncomes();
          self.incomeChosen = false;
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
