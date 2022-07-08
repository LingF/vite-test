<template>
  <div class="box">
    <h3>ref</h3>
    <div>{{ counter }}</div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const counter = ref(0)

console.log(counter) // { value: 0 }
console.log(counter.value) // 0

counter.value++
console.log(counter.value) // 1
</script>

<style scoped>

</style>