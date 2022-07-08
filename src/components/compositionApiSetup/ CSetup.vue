<template>
  <div class="box">
    <div>

    </div>
  </div>
</template>

<script setup lang="ts">
import { toRefs, toRef, defineProps } from 'vue'

const props = defineProps({
  title: String
})

// 接收参数： 1. props
//          2. context

// props（响应式的，不能解构它）
// 解构使用 toRefs
const { title } = toRefs(props)
console.log(title.value)

// 可选 prop
const name = toRef(props, 'name')
console.log(name.value)


</script>

<style scoped>

</style>