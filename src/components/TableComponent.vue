<script setup>
import TableRow from './TableRow.vue'
import { toRefs } from 'vue';

const props = defineProps({
  headers: {
    type: Array,
    required: true
  },
  rows: {
    type: Array,
    required: true
  }
})

const {headers, rows} = toRefs(props)

</script>

<template>
  <table class="table">
    <thead>
      <tr>
        <th v-for="(header, index) in headers" :key="index">{{ header }}</th>
      </tr>
    </thead>
    <tbody>
      <table-row v-for="(row, index) in rows" :key="index" :product="row" :headers="headers" />
    </tbody>
  </table>
</template>
