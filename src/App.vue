<script setup>
import { ref, onMounted, computed } from 'vue'
import TableComponent from './components/TableComponent.vue'
import SearchInput from './components/SearchInput.vue';
import InfoContainer from './components/InfoContainer.vue';
import PaginationComponent from './components/PaginationComponent.vue';
import { debounce } from './util/debounce';

const meta ={
  limit: 10,
  skip: 10,
  total: 0
};

const limit = ref(meta.limit)
const skip = ref(meta.skip);
const total = ref(0);
const search = ref('')
const products = ref([])

const loadingState = ref('loaded') // 'loading' | 'loaded' | 'error'
const baseApiUrl = 'https://dummyjson.com/products';
const fetchUrl = computed(() => {
  return `${baseApiUrl}?limit=${limit.value}&skip=${skip.value}`
})
const searchUrl = computed(() => {
  return `${baseApiUrl}/search?q=${search.value}&limit=${limit.value}&skip=${skip.value}`
})


function setMeta(meta) {
  skip.value = meta.skip
  total.value = meta.total
}

function fetchProducts(searchQuery = '') {
  loadingState.value = 'loading'
  const path = searchQuery ? searchUrl.value : fetchUrl.value
  return fetch(path)
    .then((response) => {
      return response.json()
    })
    .then((data) => {
      setMeta(data)
      products.value = data?.products || []
      return data
    })
    .catch((error) => {
      loadingState.value = 'error'
      console.log(error)
    })
    .finally(() => {
      loadingState.value = 'loaded'
    })
}

const debouncedSearch = computed(() => {
  return debounce((searchQuery) => {
    fetchProducts(searchQuery)
  }, 500)
})

onMounted(async () => {
  await fetchProducts()
})

const handleOnChange = () => {
  fetchProducts()
}

const onInputChange = (value) => {
  search.value = value
  if(value === ''){
    setMeta(meta);
  }
  debouncedSearch.value(value)
}

const onMetaChange = (meta) => {
   setMeta(meta)
   fetchProducts()
}

</script>

<template>
  <main class="container-fluid p-5">
    <div class="container mb-4">
      <div class="row">
        <div class="col">
          <select
            v-model="limit"
            v-on:change="handleOnChange"
            class="form-select"
            aria-label="Default select example"
          >
            <option selected value="10">10</option>
            <option value="20">20</option>
            <option value="30">30</option>
          </select>
        </div>
        <div class="col">
          <SearchInput v-on:search="onInputChange" />
        </div>
      </div>
    </div>

    <InfoContainer v-if="search && products.length == 0">
      <p>No products found</p>
    </InfoContainer>

    <div class="text-center p-4 loader" v-if="loadingState == 'loading'">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Loading...</span>
      </div>
    </div>
    <section v-if="loadingState == 'loaded' && products.length > 0">
      <TableComponent :data="products" />
      <div class="text-center mx-auto">
        <PaginationComponent
        :limit="limit"
        :skip="skip"
        :total="total"
        v-on:meta-change="onMetaChange"
        >
      </PaginationComponent>
      </div>
    </section>
    <InfoContainer v-if="loadingState == 'error'">
      <p>Failed to laod products, please try again</p>
      <button class="btn-primary" @click="fetchProducts">Reload</button>
    </InfoContainer>
  </main>
</template>
