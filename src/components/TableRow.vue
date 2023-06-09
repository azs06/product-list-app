<script setup>
import { toRefs, computed, h } from 'vue'
const props = defineProps({
  product: {
    type: Object,
    required: true
  },
  headers: {
    type: Array,
    required: true
  }
})

const { product, headers } = toRefs(props)

const computedProduct = computed(() => {
  const rest = {...product.value}
  Object.keys(rest).forEach(key => {
    if (!headers.value.includes(key)) {
      delete rest[key]
    }
  })  
  return rest
})
</script>
<template>
  <tr>
    <td v-for="(value, key) in computedProduct" :key="key">{{ value }}</td>
  </tr>
</template>
