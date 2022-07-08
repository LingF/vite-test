<template>
  <div class="box">
    <div>
      <p>
        state.count: {{ state.count }}
      </p>
      <p>
        obj = reactive({ count }), obj.count: {{ obj.count }}
      </p>
      <p>
        obj = reactive({})
        obj.count = count obj.count: {{ obj2.count2 }}
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
// https://v3.cn.vuejs.org/api/basic-reactivity.html#reactive

// {reactive}
import { ref, reactive, readonly, watchEffect } from 'vue'
// 响应式状态
const state = reactive({
  count: 0
})
// 返回一个响应式的对象状态
// 影响传递对象的所有嵌套 property

// Vue 响应性系统「本质」：
// 响应式状态改变时视图会自动更新


// reactive 将解包所有深层的 refs，同时维持 ref 的响应性
const count = ref(1)
const obj = reactive({ count })

// ref 会被解包
console.log('obj.count === count.value', obj.count === count.value) // true

// 它会更新 `obj.count`
count.value++
console.log('=== count.value++ ===')
console.log('[ref] count.value:', count.value) // 2
console.log('[ref] obj.count:', obj.count) // 2

// 它也会更新 `count` ref
obj.count++
console.log('=== obj.count++ ===')
console.log('[reactive] obj.count:', obj.count) // 3
console.log('[reactive] count.value:', count.value) // 3

// 当将 ref 分配给 reactive property 时，ref 将被自动解包
const count2 = ref(1)
const obj2 = reactive({})

obj2.count2 = count2
console.log('obj.count2 = count2')
console.log('obj2.count2:', obj2.count2) // 1
console.log('obj2.count2 === count2.value:', obj2.count2 === count2.value) // true


// {readonly}
// 接受一个对象 (响应式或纯对象) 或 ref 并返回原始对象的只读代理
// 只读代理是深层的：任何被访问的嵌套 property 也是只读的
const original = reactive({ count: 0 })
const copy = readonly(original)
watchEffect(() => {
  // 用于响应性追踪
  console.log('[readonly] watchEffect copy.count', copy.count)
})
// 变更 original 会触发依赖于副本的侦听器
console.log('[readonly] original.count++:', original.count++)
// 变更副本将失败并导致警告
console.log('[readonly] copy.count++:', copy.count++) // 警告!

</script>

<style scoped>

</style>