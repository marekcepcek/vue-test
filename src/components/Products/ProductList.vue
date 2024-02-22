<script lang="ts">
import { defineComponent, ref, onMounted, computed, watch } from 'vue'
import ProductTile from './ProductTile.vue'
import ProductFilter from './ProductFilter.vue'

export default defineComponent({
  name: 'ProductList',
  components: {
    ProductTile,
    ProductFilter,
  },
  setup() {
    const products = ref<Product[]>([])
    const isLoadingProducts = ref(false)
    const error = ref<string | null>(null)
    const selectedCategories = ref<string[]>([])

    onMounted(async () => {
      isLoadingProducts.value = true
      error.value = null

      try {
        const response = await fetch('https://fakestoreapi.com/products')

        if (!response.ok) {
          throw new Error(`HTTP error ${response.status}`)
        }

        const data: Product[] = await response.json()
        products.value = data
      } catch (error) {
        console.error('error loading products:', error)
        error.value = 'Error while loading products'
      } finally {
        isLoadingProducts.value = false
      }

      const urlParams = new URLSearchParams(window.location.search)
      const categoriesParam = urlParams.get('categories')
      if (categoriesParam) {
        selectedCategories.value = categoriesParam.split(',')
      }
    })

    const categories = computed(() => {
      const uniqueCategories = new Set(
        products.value.map((product) => product.category)
      )
      return Array.from(uniqueCategories).map((category) => ({
        name: category,
        productCount: products.value.filter(
          (product) => product.category === category
        ).length,
      }))
    })

    const filteredProducts = computed(() => {
      if (selectedCategories.value.length === 0) {
        return products.value
      } else {
        return products.value.filter((product) =>
          selectedCategories.value.includes(product.category)
        )
      }
    })

    const updateSelectedCategories = (newCategories: string[]) => {
      selectedCategories.value = newCategories
    }

    const title = computed(() => {
      return selectedCategories.value.length > 0
        // Title too long :(
        // ? `products in: ${selectedCategories.value.join(', ')}`
        ? 'Filtered products'
        : 'All Products'
    })

    watch(selectedCategories, () => {
      filteredProducts.value = products.value.filter((product) => {
        return (
          selectedCategories.value.length === 0 ||
          selectedCategories.value.includes(product.category)
        )
      })
    })

    return {
      products,
      isLoadingProducts,
      error,
      categories,
      updateSelectedCategories,
      filteredProducts,
      title,
    }
  },
})

interface Product {
  id: number
  title: string
  price: number
  description: string
  category: string
  image: string
  rating: {
    rate: number
    count: number
  }
}
</script>

<template>
  <div class="title-wrap">
    <h1 class="title">{{ title }}</h1>
    <product-filter
      :categories="categories"
      @update:selected="updateSelectedCategories"
    />
  </div>

  <ul class="product__list" v-if="!isLoadingProducts">
    <product-tile
      v-for="product in filteredProducts"
      :key="product.id"
      :product="product"
    />
  </ul>

  <div
    v-else-if="isLoadingProducts"
    class="products__loading"
    title="Loading products"
  >
    <svg
      width="46"
      height="46"
      class="products__loading__spinner"
      viewBox="0 0 24 24"
    >
      <path
        d="M12,1A11,11,0,1,0,23,12,11,11,0,0,0,12,1Zm0,19a8,8,0,1,1,8-8A8,8,0,0,1,12,20Z"
        opacity=".25"
      />
      <path
        d="M10.14,1.16a11,11,0,0,0-9,8.92A1.59,1.59,0,0,0,2.46,12,1.52,1.52,0,0,0,4.11,10.7a8,8,0,0,1,6.66-6.61A1.42,1.42,0,0,0,12,2.69h0A1.57,1.57,0,0,0,10.14,1.16Z"
        class="moving__part"
      />
    </svg>
  </div>

  <div className="error__notice" v-else-if="error">
    <p>An error 🫨🥵 during loading occurred: {{ error }}</p>
    <!-- Lazyyyyyyyyy -->
    <button @click="window.location.reload()">Refresh</button>
  </div>
</template>

<style scoped lang="scss">
.product__list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(24rem, 100%), 1fr));
  align-items: stretch;
  gap: 2em;
  margin-top: 2em;
}
.products__loading {
  margin-top: 3em;

  &__spinner {
    display: block;
    margin: 0 auto;

    .moving__part {
      transform-origin: center;
      animation: spinner_animation 0.75s infinite linear;
    }
    @keyframes spinner_animation {
      to {
        transform: rotate(360deg);
      }
    }
  }
}

.error__notice {
  display: flex;
  flex-direction: column;
  width: 100%;
  align-items: center;
  gap: 1em;
  margin-top: 1em;
}

.title-wrap {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  flex-wrap: wrap;
  gap: 2em;
}
</style>
