<template>
  <div class="about">
    <h2>composition-api-introduction</h2>

    <!-- 1. ref -->
    <CRef />
    <!-- 2. lifeCycle -->
    <CLifeCycle v-if="isShow" @close="isShow = false" />
    <!-- 3. watch -->
    <CWatch v-if="isShow" @close="isShow = false" />
    <!-- 4. computed -->
    <CComputed />

    <!-- next-to reactivity-fundamentals -->
    <CBasicReactivity />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import CRef from '@/components/compositionApiIntroduction/CRef.vue'
import CLifeCycle from '@/components/compositionApiIntroduction/CLifeCycle.vue'
import CWatch from '@/components/compositionApiIntroduction/CWatch.vue'
import CComputed from '@/components/compositionApiIntroduction/CComputed.vue'
import CBasicReactivity from '@/components/reactivityFundamentals/CBasicReactivity.vue'

const isShow = ref(true)
</script>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}

.box {
  margin: 10px 0;
  padding: 10px 15px;
  border: 1px solid #00bd7e;
}
</style>
