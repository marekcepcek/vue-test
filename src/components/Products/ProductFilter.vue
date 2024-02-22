<script lang="ts">
import { defineComponent, ref, PropType, watchEffect, computed } from 'vue'

export default defineComponent({
  props: {
    categories: {
      type: Array as PropType<{ name: string; productCount: number }[]>,
      required: true,
    },
  },

  emits: ['update:selected'],

  setup(props, { emit }) {
    const selectedCategories = ref<string[]>([])

    const sortedCategories = computed(() => {
      return [...props.categories].sort(
        (a, b) => b.productCount - a.productCount
      )
    })

    const onCategoryChange = (category: string) => {
      if (selectedCategories.value.includes(category)) {
        selectedCategories.value = selectedCategories.value.filter(
          (cat) => cat !== category
        )
      } else {
        selectedCategories.value = [...selectedCategories.value, category]
      }

      emit('update:selected', [...selectedCategories.value])
      updateUrl()
    }

    const resetFilters = () => {
      selectedCategories.value = []
      emit('update:selected', [])
      updateUrl()
    }

    const updateUrl = () => {
      const url = new URL(window.location.href)
      if (selectedCategories.value.length > 0) {
        url.searchParams.set('categories', selectedCategories.value.join(','))
      } else {
        url.searchParams.delete('categories')
      }
      history.pushState({}, '', url.toString())
    }

    watchEffect(() => {
      const urlParams = new URLSearchParams(window.location.search)
      const categoriesParam = urlParams.get('categories')
      if (categoriesParam) {
        selectedCategories.value = categoriesParam.split(',')
      }
    })

    return {
      selectedCategories,
      onCategoryChange,
      resetFilters,
      sortedCategories,
    }
  },
})
</script>

<template>
  <div class="product-filter">
    <div class="checkbox-container">
      <div
        class="checkbox"
        v-for="category in sortedCategories"
        :key="category.name"
      >
        <input
          type="checkbox"
          :id="category.name"
          :value="category.name"
          :checked="selectedCategories.includes(category.name)"
          @change="onCategoryChange(category.name)"
        />
        <label :for="category.name">{{
          `${category.name} (${category.productCount})`
        }}</label>
      </div>
    </div>
    <button class="btn" v-if="selectedCategories.length > 0" @click.prevent="resetFilters">Reset Filters</button>
  </div>
</template>

<style scoped lang="scss">
.product-filter {
	display: flex;
	justify-content: flex-start;
	align-items: center;
  gap: 1.5em;

	@media only screen and (min-width: 960px) {
		justify-content: flex-end;
	}

  .checkbox-container {
    display: flex;
    justify-content: flex-start;
    flex-wrap: wrap;
    gap: 1em;

    .checkbox {
      display: inline-block;
      width: max-content;
      max-width: 100%;

      input {
        height: initial;
        width: initial;
        display: none;
        cursor: pointer;
      }

      label {
        position: relative;
        cursor: pointer;
        text-transform: initial;

        &:before {
          content: '';
          -webkit-appearance: none;
          background-color: transparent;
          border: 0.2em solid #000;
          box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05),
            inset 0px -15px 10px -12px rgba(0, 0, 0, 0.05);
          padding: 7px;
          display: inline-block;
          position: relative;
          vertical-align: middle;
          cursor: pointer;
          margin-right: 0.5em;
        }
      }

      input:checked + label:after {
        content: '';
        display: block;
        position: absolute;
        top: 3px;
        left: 7px;
        width: 4px;
        height: 8px;
        border: solid #fff;
        border-width: 0 3px 3px 0;
        transform: rotate(45deg);
      }
      input:checked ~ label:before {
        background: #000;
      }
    }
  }
}
</style>
