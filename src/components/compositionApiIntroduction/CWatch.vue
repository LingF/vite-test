<template>
  <div class="box">
    <div @click="counter++, state.count++, count2++">watch:
      <p>- counter: {{ counter }}</p>
      <p>- count: {{ state.count }}</p>
      <p>- count2: {{ count2 }}</p>
    </div>
    <button @click="changeValues">多个数据源改变</button>
    <button @click="changeEveryValues">多个数据源逐个改变</button>

    <div style="margin-top: 10px">
      <p>
        - numbers: {{ numbers }}
      </p>
    </div>

    <div style="margin-top: 10px">
      <p @click="eCount++">
        - eCount: {{ eCount }}
      </p>
    </div>

    <button @click="emit('close')">关闭组件</button>

    <div style="margin-top: 10px">
      <p @click="flushCount++" ref="flushDom">
        - flushCount: {{ flushCount }}
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
import _ from 'lodash'
import { ref, reactive, watch, watchEffect, nextTick } from 'vue'

const counter = ref(0)
watch(counter, (newValue, oldValue) => {
  console.log('The new counter value is: ' + counter.value)
})

// 数据源
// 1. 返回值的 getter 函数
const state = reactive({ count: 0 })
// const { count } = toRefs(state)
watch(
  () => state.count,
  (count, prevCount) => {
    console.log('The new state.count value is: ' + state.count)
  }
)
// 2. ref
const count2 = ref(0)
watch(count2, (count, prevCount) => {
  console.log('The new count2 value is: ' + count)
})


// {多个数据源}
const firstName = ref('')
const lastName = ref('')

watch([firstName, lastName], (newValues, prevValues) => {
  console.log('firstName, lastName', newValues, prevValues)
})

// 多个同步更改只会触发一次侦听器
const changeValues = () => {
  firstName.value = 'John'
  lastName.value = 'Smith'
}

// 强制每个更改都触发侦听器
// 1. nextTick
const changeEveryValues = async () => {
  firstName.value = 'Bill'
  await nextTick()
  lastName.value = 'Gz'
}
// 2. 通过更改 watch 设置 flush: 'sync'


// {侦听响应式对象}
// 使用侦听器来比较一个数组或对象的值，要求它有一个由值构成的副本
const numbers = reactive([1, 2, 3, 4])

watch(
  () => [...numbers],
  (number, prevNumbers) => {
    console.log('numbers, prevNumbers',numbers, prevNumbers)
  }
)
numbers.push(5)

// 深度嵌套对象或数组中的 property 变化，仍需 deep
const user = reactive({
  id: 1,
  attributes: {
    name: ''
  }
})

watch(
  () => user,
  (user, prevUser) => {
    console.log('[watch] <no deep> user, prevUser', state.attributes.name, prevUser.attributes.name)
  }
)

watch(
  () => user,
  (user, prevUser) => {
    console.log('[watch] %c <deep> %c user, prevUser', 'color: #fcb127;', user.attributes.name, prevUser.attributes.name)
    // 对象的当前值, 上一个状态值的引用
    // 所以并不能获得 旧值
  },
  { deep: true }
)

user.attributes.name = 'Alex'
// 侦听一个响应式对象或数组将始终返回该对象的当前值和上一个状态值的引用
// 为了完全侦听深度嵌套的对象和数组，可能需要对值进行深拷贝

watch(
  () => _.cloneDeep(user),
  (user, prevUser) => {
    console.log('[watch] %c <deep & cloneDeep> %c user, prevUser', 'color: #fcb127;', user.attributes.name, prevUser.attributes.name)
  }
)

user.attributes.name = 'Bill'


// {watchEffect}
// 为了根据响应式状态 自动应用 和 重新应用 副作用
// https://v3.cn.vuejs.org/guide/reactivity-computed-watchers.html#watcheffect
const eCount = ref(0)

// 立即执行转入的函数，响应式追踪其依赖
watchEffect(() => console.log('[watchEffect] eCount: ', eCount.value))

// ？
setTimeout(() => {
  eCount.value++
  // -> logs watchEffect eCount: 1
}, 100)

// {停止侦听}
// 在组件的 setup() 函数或生命周期钩子被调用时
// 侦听器会被链接到该组件的生命周期，并在组件卸载时自动停止
const stop = watchEffect(() => {
  console.log('[watchEffect] stop')
})

// // 显式调用返回值以停止侦听
// stop()

// {清除副作用}
// 副作用函数会执行一些异步操作，可能在完成之前状态已经改变
// 所以副作用函数可以接受一个 onInvalidate 函数入参，清理失效时的回调
// 失效回调触发时机
// - 副作用即将重新执行
// - 侦听器被停止（setup、生命周期钩子中使用，则为组件卸载时）
watchEffect(onInvalidate => {
  let timer
  // = setInterval(() => {
  //   console.log('[watchEffect]%c <onInvalidate>', 'color: #fcb127;')
  // }, 1000)
  onInvalidate(() => {
    console.log('[watchEffect]%c onInvalidate done', 'color: red;');
    timer && clearInterval(timer)
  })
})
const emit = defineEmits(['close'])
// onInvalidate
// 通过传入一个函数去注册失效回调，而不是从回调返回它，是因为返回值对于异步错误处理很重要
// 在执行数据请求时，副作用函数往往是一个异步函数
// 我们知道异步函数都会隐式地返回一个 Promise，但是清理函数必须要在 Promise 被 resolve 之前被注册
// Vue 依赖这个返回的 Promise 来自动处理 Promise 链上的潜在错误

// {副作用 _刷新时机_}
// 缓存并异步刷新（执行），避免同一个 “tick” 中多个状态改变导致重复调用
// 组件的 update 也是一个被侦听的副作用
// 当自定义的副作用函数进入队列时，默认会在所有组件 update 前执行
const flushCount = ref(0)
const flushDom = ref()
// 1.
console.log('flushCount', flushCount.value)
console.log('flushDom', flushDom.value);

watchEffect(() => {
  // 2. 3.
  console.log('[watchEffect] flushCount', flushCount.value)
  console.log('[watchEffect] flushDom', flushDom.value)
})
// 结论：
// 1、2 都在一开始执行，dom 未被创建
// 3 执行是因为 watchEffect 响应式追踪其依赖，依赖的 flushDom 创建成功
// 所以再次触发了副作用，此时输出 dom 存在

// 组件更新后运行副作用
// flush: pre(默认) | post | sync(强制效果始终同步触发，低效不常用)
watchEffect(() => {
  console.log('[watchEffect] %c <post> flushCount', 'color: #fcb127;', flushCount.value)
  console.log('[watchEffect] %c <post> flushDom', 'color: #fcb127;', flushDom.value)
}, {
  flush: 'post'
})

// [dev] {侦听器调试}
// - onTrack 将在响应式 property 或 ref 作为依赖项被追踪时被调用
// - onTrigger 将在依赖项变更导致副作用被触发时被调用
watchEffect(
  () => {
    /* 副作用 */
    console.log(flushCount.value)
    console.log('[watchEffect] onTrigger')
  },
  {
    onTrigger(e) {
      console.log('onTrigger done', e)
      debugger
    }
  }
)
</script>

<style scoped>

</style>