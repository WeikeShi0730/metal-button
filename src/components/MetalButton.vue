<script setup>
import { onMounted, ref } from 'vue'

const isClick = ref(false)
const container = ref(null)
const fingerprints = ref([])

onMounted(async () => {
  const stream = await navigator.mediaDevices.getUserMedia({
    video: true,
    audio: false
  })
  const video = container.value
  video.srcObject = stream
  video.onloadedmetadata = () => {
    video.play()
  }
})

const handleClick = (e) => {
  isClick.value = true
  const btn = container.value.getBoundingClientRect()
  const id = Math.random().toString(16).slice(2)
  const fingerprint = {
    x: e.clientX - btn.left,
    y: e.clientY - btn.top,
    id
  }
  fingerprints.value.push(fingerprint)
}
</script>

<template>
  <div class="w-full h-full relative flex justify-center items-center">
    <div
      :class="[isClick ? 'scale-95' : '', 'h-48 w-96 overflow-hidden rounded-full']"
      @pointerdown="handleClick"
      @pointerup="isClick = false"
    >
      <div
        class="fingerprint z-50 absolute left-0 top-0 -translate-x-1/2 -translate-y-1/2 w-16 h-16 overflow-hidden rounded-full bg-zinc-300/40 blur-2xl"
        v-for="fingerprint in fingerprints"
        :key="fingerprint.id"
        :style="{
          left: fingerprint.x + 'px',
          top: fingerprint.y + 'px'
        }"
      ></div>
      <video
        class="button-reflection object-cover opacity-70 blur-sm saturate-50 brightness-110"
        ref="container"
      ></video>
    </div>
  </div>
</template>

<style scoped>
.button-reflection {
  -webkit-transform: scaleX(-1);
  transform: scaleX(-1);
}
.fingerprint {
  mask-image: radial-gradient(50% 50% at 50% 50%, #000000 0%, rgba(0, 0, 0, 0) 100%);
  -webkit-mask-image: radial-gradient(50% 50% at 50% 50%, #000000 0%, rgba(0, 0, 0, 0) 100%);
}
</style>
