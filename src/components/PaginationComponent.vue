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

const metaData = reactive({
  ...meta
})

const {limit, skip, total} = toRefs(meta)

const totalPages = computed(() => {
  return Math.ceil(total.value / limit.value)
})

const computedMeta = computed(() => {
  return {
    limit: metaData.limit,
    skip: metaData.skip,
    total: metaData.total
  }
})

const onPageSelect = (page) => {
  metaData.limit = page * limit.value 
  emit('meta-change', computedMeta.value)
}

const onNext = () => {
  metaData.skip = metaData.skip + limit.value
  emit('meta-change', computedMeta.value)
}

const onPrev = () => {
  metaData.skip = metaData.skip - limit.value
  emit('meta-change', computedMeta.value)
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
