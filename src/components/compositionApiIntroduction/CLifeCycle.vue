<template>
  <div class="box">
    <div ref="el"></div>
    <button id="count" @click="count++">{{ count }}</button>
    <button @click="emit('close')">关闭</button>
    <TErrorChild />

    <keep-alive>
      <TKeepAliveChild v-if="isKeepAliveShow" />
    </keep-alive>
    <button @click="isKeepAliveShow = !isKeepAliveShow">keep alive switch</button>
  </div>
</template>

<script setup lang="ts">
import { ref, defineEmits, onMounted, onUpdated, onUnmounted, onBeforeMount, onBeforeUpdate, onBeforeUnmount, onErrorCaptured, onRenderTracked, onRenderTriggered, onActivated, onDeactivated, onServerPrefetch } from 'vue'
import TErrorChild from './assist/TErrorChild.vue'
import TKeepAliveChild from './assist/TKeepAliveChild.vue'

// 在组合式API（Composition API）中，created() 和 beforeCreated() 不可访问。被替换为 setup() ，在 created() 和 beforeCreated() 实现的逻辑完全可以迁移到 setup() 中


// _挂载完成_ 后执行
// - 其所有同步子组件已被挂载（不包含异步组件或<Suspense>树内的组件）
// - 其自身 DOM 树已创建并插入父容器
const el = ref()

let intervalId // onUnmounted

onMounted(() => {
  console.log('%c「生命周期」%c onMounted done', 'color: #00bd7e;', 'color: red;')
  console.log('el.value', el.value)
  intervalId = setInterval(() => { // onUnmounted
    console.log('setInterval');
  }, 500)
})


// 因为响应式状态变更而更新其 DOM 树之后调用
// 访问更新后的 DOM，使用 nextTick()
const count = ref(0)

onUpdated(() => {
  console.log('%c「生命周期」%c onUpdated done', 'color: #00bd7e;', 'color: red;')
  console.log(document.getElementById('count')?.textContent)
})


// _卸载之后_ 调用
// - 所有子组件已被卸载
// - 所有相关的响应式作用（渲染作用以及 setup() 时创建的计算属性和侦听器）都已经停止
const emit = defineEmits(['close'])

onUnmounted(() => {
  console.log('%c「生命周期」 onUnmounted done', 'color: #00bd7e;', 'color: red;')
  clearInterval(intervalId)
})


// 组件 _挂载之前_ 调用
// 组件已经完成了其响应式状态的设置，但还没有创建 DOM 节点
// 即将首次执行 DOM 渲染过程
onBeforeMount(() => {
  console.log('%c「生命周期」%c onBeforeMount done', 'color: #00bd7e;', 'color: red;')
  console.log('count', count) // 响应式状态
  console.log('el.value', el.value) // DOM
})


// 组件即将因为 _响应式状态变更而更新其 DOM 树之前_ 调用
// 在这个钩子中更改状态也是安全的
onBeforeUpdate(() => {
  console.log('%c「生命周期」%c onBeforeUpdate done', 'color: #00bd7e;', 'color: red;')
  el.value.innerHTML = 'onBeforeUpdate'
})


// 组件实例被 _卸载之前_ 调用
onBeforeUnmount(() => {
  console.log('%c「生命周期」%c onBeforeUnmount done', 'color: #00bd7e;', 'color: red;')
  console.log('intervalId', intervalId)
})


// 捕获后代组件传递的 _错误_ 时调用
// - 组件渲染
// - 事件处理器
// - 生命周期钩子
// - setup() 函数
// - 侦听器
// - 自定义指令钩子
// - 过渡钩子
// https://staging-cn.vuejs.org/api/composition-api-lifecycle.html#onerrorcaptured
onErrorCaptured((err, instance, info) => {
  console.log('%c「生命周期」 %c onErrorCaptured done', 'color: #00bd7e;', 'color: red;')
  console.log('err', err) // 错误对象
  console.log('instance', instance) // 触发该错误的组件实例
  console.log('info', info) // 说明错误来源类型的信息字符串
  return false // 通过返回 false 来阻止错误继续传递。即表示“这个错误已经被处理了，应当被忽略”
})


// [dev] _当响应式依赖被组件的渲染作用 *追踪后* _ 调用
onRenderTracked(e => {
  console.log('%c「生命周期」%c DEV | onRenderTracked done', 'color: #00bd7e;', 'color: red;')
  console.log('effect', e.effect)
  console.log('target', e.target)
  console.log('type', e.type) /* 'get' | 'has' | 'iterate' */
  console.log('key', e.key)
})


// [dev] _当响应式依赖触发了组件渲染作用的 *运行之后* _ 调用
onRenderTriggered(e => {
  console.log('%c「生命周期」%c DEV | onRenderTriggered done', 'color: #00bd7e;', 'color: red;')
  console.log('effect', e.effect)
  console.log('target', e.target)
  console.log('type', e.type) /* 'get' | 'has' | 'iterate' */
  console.log('key', e.key)
  console.log('newValue?', e.newValue)
  console.log('oldValue?', e.oldValue)
  console.log('oldTarget?', e.oldTarget)
})


// 组件实例是 <KeepAlive> 缓存树的一部分，当组件被 _插入到 DOM 中时_ 调用
let isKeepAliveShow = ref(true)
onActivated(() => {
  console.log('%c「生命周期」%c onActivated done', 'color: #00bd7e;', 'color: red;')
})


// 组件实例是 <KeepAlive> 缓存树的一部分，当组件 _从 DOM 中被移除时_ 调用
onDeactivated(() => {
  console.log('%c「生命周期」%c onDeactivated done', 'color: #00bd7e;', 'color: red;')
})


// [SSR only] 组件实例在服务器上被渲染之前调用
onServerPrefetch(() => {
  console.log('%c「生命周期」%c SSR only | onRenderTriggered done', 'color: #00bd7e;', 'color: red;')
})
</script>

<style scoped>

</style>