<script setup>
import {RouterLink, RouterView} from 'vue-router'

</script>

<template>
  <header>
    <div class="d-flex justify-content-between">
      <div>Inventory</div>
      <div class="current-date">
        <div>{{ currentDay }}</div>
        <div>{{ currentDate }}</div>
      </div>
    </div>
  </header>
  <div class="main">
    <nav class="navbar navbar-expand-sm bg-light navbar-dark">
      <ul class="navbar-nav">
        <li class="nav-item m-1">
          <RouterLink class="btn btn-light btn-outline-primary" to="/">Incomes</RouterLink>
        </li>
        <li class="nav-item m-1">
          <RouterLink class="btn btn-light btn-outline-primary" to="/products">Products</RouterLink>
        </li>
      </ul>
    </nav>
    <div class="description">

    </div>
    <RouterView/>
  </div>
</template>
<script>
export default {
  data() {
    return {
      currentDate: '',
      currentDay: ''
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
            minute: '2-digit',
            second: '2-digit'
          };

      this.currentDate = currentDate.toLocaleString('en-us', dateOptions).replace('at', '');
      this.currentDay = currentDate.toLocaleString('en-en', {weekday: 'long'});

    }
  },
  mounted() {
    setInterval(() => this.getCurrentDate(), 1000)
  }
}
</script>
