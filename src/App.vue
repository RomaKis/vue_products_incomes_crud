<script setup>
import { RouterLink, RouterView } from 'vue-router'

</script>

<template>
  <header>
    <div class="d-flex justify-content-between">
      <div>Inventory</div>
      <div>Some date</div>
    </div>
  </header>
  <div class="main">
    <nav class="navbar navbar-expand-sm bg-light navbar-dark">
      <ul class="navbar-nav">
        <li class="nav-item m-1">
          <RouterLink class="btn btn-light btn-outline-primary" to="/">Income</RouterLink>
        </li>
        <li class="nav-item m-1">
          <RouterLink class="btn btn-light btn-outline-primary" to="/product">Product</RouterLink>
        </li>
      </ul>
    </nav>
    <RouterView />
  </div>
</template>
