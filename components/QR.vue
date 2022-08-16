<!-- https://www.npmjs.com/package/@chenfengyuan/vue-qrcode -->
<script setup lang="ts">
import { ref } from 'vue'
import VueQrcode from '@chenfengyuan/vue-qrcode';


const props = defineProps({
  qrText: {
    default: "text",
  },
  qrWidth : {
    default: 300,
  },
})

const qrText = ref(props.qrText)
const qrWidth = ref(props.qrWidth)

</script>

<template>
    <vue-qrcode :value=qrText :options="{ width: qrWidth }"></vue-qrcode>
</template>