<script setup lang="ts">
import { ref } from 'vue'

const props = defineProps({
  count: {
    default: 0,
  },
})

const counter = ref(props.count)

const random = () => {
  counter.value =  Math.floor(Math.random() * 100)
}
</script>

<template>
  <div flex="~" w="min" border="~ gray-400 opacity-50 rounded-md">
    <button
      border="r gray-400 opacity-50"
      p="2"
      font="mono"
      outline="!none"
      hover:bg="gray-400 opacity-20"
      @click="random"
    >
      Random
    </button>
    <span m="auto" p="2">{{ counter }}</span>
  </div>
</template>
