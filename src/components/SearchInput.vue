<script setup>
import {ref} from 'vue'
const emit = defineEmits(['search'])
const search = ref('')
const onInputChange = (event) => {  
  emit('search', event.target.value)
}
const clearSearch = () => {
  search.value = ''
  emit('search', '')
}
</script>

<template>
   <div class="input-group">
        <input
        v-model="search"
        v-on:input="onInputChange"
        type="search"
        class="form-control"
        placeholder="search product"
    />
    <button v-if="search" @click="clearSearch" class="btn btn-outline-secondary" type="button">clear</button>
   </div> 

</template>
