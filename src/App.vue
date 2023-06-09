<script setup>
import { ref, onMounted, computed } from 'vue'
import TableComponent from './components/TableComponent.vue'
import SearchInput from './components/SearchInput.vue'
import InfoContainer from './components/InfoContainer.vue'
import PaginationComponent from './components/PaginationComponent.vue'
import { debounce } from './util/debounce'

// maybe we keep this in .env file
const baseUrl = 'https://dummyjson.com/products'
const tableHeaders = [
  'title',
  'description',
  'price',
  'discount', //discountPercentage
  'rating',
  'stock',
  'brand',
  'category'
]
const defaultMeta = {
  limit: 10,
  skip: 0,
  total: 0
}
const limit = ref(defaultMeta.limit)
const skip = ref(defaultMeta.skip)
const total = ref(defaultMeta.total)

const products = ref([])
const search = ref('')
const loadingState = ref('loaded') // loading, loaded, error

const fetchUrl = computed(() => {
  return `${baseUrl}?limit=${limit.value}&skip=${skip.value}`
})

const searchUrl = computed(() => {
  return `${baseUrl}/search?q=${search.value}&limit=${limit.value}`
})

const fetchProducts = (searchQuery = '') => {
  loadingState.value = 'loading'
  const url = searchQuery ? searchUrl.value : fetchUrl.value
  return fetch(url)
    .then((response) => response.json())
    .then((data) => {
      setMeta(data)
      products.value = data.products
    })
    .catch((error) => {
      loadingState.value = 'error'
      console.log(error)
    })
    .finally(() => {
      loadingState.value = 'loaded'
    })
}

const handleOnChange = () => {
  fetchProducts()
}

const debeouncedSearch = debounce((searchInput) => {
  fetchProducts(searchInput)
}, 500)

const handleOnSearch = (searchInput) => {
  search.value = searchInput
  if (searchInput == '') {
    setMeta(defaultMeta);
  }
  debeouncedSearch(searchInput)
}

const setMeta = (meta) => {
  skip.value = meta.skip || skip.value
  total.value = meta.total || total.value
}

const hadlePagination = (meta) => {
  setMeta(meta)
  fetchProducts()
}

onMounted(async () => {
  await fetchProducts()
})
</script>

<template>
  <main class="container-fluid p-4">
    <section class="content-header">
      <div class="container">
        <div class="row">
          <div class="col">
            <select
              v-on:change="handleOnChange"
              class="form-select"
              v-model="limit"
              aria-label="Default select example"
            >
              <option selected value="10">10</option>
              <option value="20">20</option>
              <option value="30">30</option>
            </select>
          </div>
          <div class="col">
            <SearchInput v-on:search="handleOnSearch"></SearchInput>
          </div>
        </div>
      </div>
    </section>
    <section class="content-body">
      <InfoContainer v-if="search && products.length == 0">
        <p>No products found</p>
      </InfoContainer>
      <div class="text-center p-4 loader" v-if="loadingState == 'loading'">
        <div class="spinner-border text-primary" role="status">
          <span class="visually-hidden">Loading...</span>
        </div>
      </div>
      <template v-if="products.length > 0 && loadingState == 'loaded'">
        <table-component :headers="tableHeaders" :rows="products" />
        <PaginationComponent
          :limit="limit"
          :skip="skip"
          :total="total"
          v-on:meta-change="hadlePagination"
          ></PaginationComponent>
      </template>
    </section>
  </main>
</template>
