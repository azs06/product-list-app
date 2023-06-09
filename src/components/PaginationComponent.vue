<script setup>
import { computed, toRefs, reactive } from 'vue'
const emit = defineEmits(['meta-change']);

const meta = defineProps({
  limit: {
    type: Number,
    required: true
  },
  skip: {
    type: Number,
    required: true
  },
  total: {
    type: Number,
    required: true
  }
})
const { limit, skip, total } = toRefs(meta)

const totalPages = computed(() => {
  return Math.ceil(total.value / limit.value)
})


const onPageSelect = (page) => {
  console.log(page)
  emit('meta-change', { ...meta, skip: page * limit.value })
}

const onNext = () => {
  emit('meta-change', {...meta, skip: meta.skip + limit.value})
}

const onPrev = () => {
  emit('meta-change', {...meta, skip: meta.skip})
}
const isFirstPage = computed(() => {
  return skip.value - limit.value <= 0;
})
const isLastPage = computed(() => {
  return skip.value + limit.value >= total.value
})

const activePageIndex = computed(() => {
  if(isFirstPage.value) return 1
  return skip.value / limit.value
})


</script>

<template>
  <nav aria-label="Page navigation example">
    <ul class="pagination">
      <li v-if="!isFirstPage" class="page-item"><a class="page-link" @click.prevent="onPrev" href="#">Previous</a></li>
      <li v-for="(_, index) in totalPages" :key="index" class="page-item">
        <a class="page-link" :class="{active: activePageIndex === index + 1}" @click="() => onPageSelect( index + 1)" href="#">{{ index + 1 }}</a>
      </li>
      <li v-if="!isLastPage" class="page-item"><a class="page-link" href="#" @click.prevent="onNext">Next</a></li>
    </ul>
  </nav>
</template>
