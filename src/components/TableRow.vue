<script setup>
import { toRefs, computed } from 'vue';
const props = defineProps({
  rowData: {
    type: Object,
    required: true
  }
})
const { rowData } = toRefs(props);

const description = computed(() => {
  return rowData.value?.description?.slice(0, 25) + '...'
})

</script>

<template>
  <tr>
    <th scope="row">{{ rowData?.title }}</th>
    <td>{{ rowData?.description }}</td>
    <td>{{ rowData?.price }}</td>
    <td>{{ rowData?.rating}}</td>
    <td> {{ rowData?.stock }} </td>
    <td> {{ rowData?.brand }} </td>
    <td> {{ rowData?.category }} </td>
    <td> {{ description }} </td>
  </tr>
</template>
