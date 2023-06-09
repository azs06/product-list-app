<script setup>
import { toRefs } from 'vue'
import TableRow from './TableRow.vue'
const props = defineProps({
  data: {
    type: Array,
    required: true
  }
})
const { data } = toRefs(props)
</script>
<template>
  <table class="table table-light">
    <thead>
      <tr>
        <th scope="col">Title</th>
        <th scope="col">Description</th>
        <th scope="col">Price</th>
        <th scope="col">Rating</th>
        <th scope="col">Stock</th>
        <th scope="col">Brand</th>
        <th scope="col">Category</th>
        <th scope="col">Discount</th>
      </tr>
    </thead>
    <tbody>
      <TableRow v-for="item in data" :rowData="item" :key="item.id" />
    </tbody>
  </table>
</template>
