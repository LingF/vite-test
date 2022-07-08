<template>
  <div>
    keep alive 子组件

    <TGrandson v-if="isShow" />
    <button @click="isShow = !isShow">孙子组件switch</button>
  </div>
</template>

<script setup lang="ts">
import { ref, onActivated, onDeactivated } from 'vue'
import TGrandson from './TGrandson.vue'

let isShow = ref(true)

// 当前组件被 keep-alive 包裹则会触发
onActivated(() => {
  console.log('%c「子组件生命周期」%c onActivated done', 'color: #00bd7e;', 'color: red;')
})

onDeactivated(() => {
  console.log('%c「子组件生命周期」%c onDeactivated done', 'color: #00bd7e;', 'color: red;')
})
</script>

<style scoped>

</style>