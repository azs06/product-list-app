<script setup>
const props = defineProps({
  cssClass: {
    type: String,
    required: false
  }
})
const cssClass = props.cssClass || '';
</script>

<template>
  <div class="text-center p-4" :class="{cssClass}">
    <slot></slot>
  </div>
</template>
