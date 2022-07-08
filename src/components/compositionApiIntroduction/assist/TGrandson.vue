<template>
  <div>
    孙子组件
  </div>
</template>

<script setup lang="ts">
import { onActivated, onDeactivated } from 'vue'

// 当前组件是 keep-alive 包裹的根组件的后代组件
// 父组件内对当前组件挂载或卸载并不会触发
// 只有 keep-alive 包裹的根组件插入移除，其本身或后代才会触发
// https://staging-cn.vuejs.org/guide/built-ins/keep-alive.html#lifecycle-of-cached-instance
onActivated(() => {
  console.log('%c「后代组件生命周期」%c onActivated done', 'color: #00bd7e;', 'color: red;')
})

onDeactivated(() => {
  console.log('%c「后代组件生命周期」%c onDeactivated done', 'color: #00bd7e;', 'color: red;')
})
</script>

<style scoped>

</style>