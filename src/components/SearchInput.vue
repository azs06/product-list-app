<script setup>
import { ref, defineEmits } from "vue";

const emit = defineEmits(["search"]);
const searchQuery = ref("");


const handleOnInput = () => {
    emit("search", searchQuery.value);
}

const clearSearch = () => {
    searchQuery.value = "";
    emit("search", searchQuery.value);
}

</script>

<template>
  <div class="input-group mb-3">
    <input
      v-model="searchQuery"  
      type="search"
      class="form-control"
      placeholder="Search"
      aria-label="Search"
      v-on:input="handleOnInput"
    />
    <span class="input-group-text" v-on:click="clearSearch" id="basic-addon2">Clear</span>
  </div>
</template>
