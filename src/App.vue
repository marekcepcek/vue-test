<script setup lang="ts">
  import ProductList from './components/Products/ProductList.vue';
</script>

<template>
  <section class="filter__section">
    <div class="wrapper">
      <product-list />
    </div>
  </section>
</template>

<style scoped lang="scss">
  .filter__section {
    padding-block: 8rem;
  }
</style>
