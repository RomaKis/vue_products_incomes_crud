<script setup>
import {RouterLink, RouterView} from 'vue-router'

</script>

<template>
  <header>
    <div class="header">
      <div class="header-logo">INVENTORY</div>
      <div class="current-date">
        <div>Today</div>
        <div>{{ currentDate }}</div>
      </div>
    </div>
  </header>
  <div class="main">
    <div class="navb">
      <ul class="navbar-nav">
        <li class="nav-item ">
          <RouterLink class="btn-incomes" to="/">Incomes</RouterLink>
        </li>
        <li class="nav-item">
          <RouterLink class="btn-product" to="/products">Products</RouterLink>
        </li>
      </ul>
    </div>
    <div class="description">

    </div>
    <RouterView/>
  </div>
</template>
<script>
export default {
  data() {
    return {
      currentDate: ''
    }
  },
  methods: {
    getCurrentDate: function () {
      let currentDate = new Date(),
          dateOptions = {
            year: 'numeric',
            month: 'long',
            day: 'numeric',
            hour: '2-digit',
            minute: '2-digit'
          };

      this.currentDate = currentDate.toLocaleString('en-us', dateOptions).replace('at', '');
    }
  },
  mounted() {
    setInterval(() => this.getCurrentDate(), 1000)
  }
}
</script>
