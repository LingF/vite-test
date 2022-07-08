<template>
  <div class="box">
    <div>
      <p>count: {{ count }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue'
const count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: val => {
    count.value = val - 1
  }
})

plusOne.value = 1
console.log(count.value)

// [dev] {调试 Computed}
// - onTrack 会在某个响应式 property 或 ref 作为依赖被追踪时调用
// - onTrigger 会在侦听回调被某个依赖的修改触发时调用
const plusTwo = computed(() => count.value + 2, {
  onTrack(e) {
    // 当 count.value 作为依赖被追踪时触发
    debugger
  },
  onTrigger(e) {
    // 当 count.value 被修改时触发
    debugger
  }
})
// 访问 plusOne，应该触发 onTrack
// console.log(plusTwo.value)
// 修改 count.value，应该触发 onTrigger
// count.value++
</script>

<style scoped>

</style>