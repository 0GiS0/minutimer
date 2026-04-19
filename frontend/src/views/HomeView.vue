<script setup lang="ts">
import { ref, computed, onUnmounted } from 'vue'

// Timer state
const elapsedSeconds = ref(0)
const isRunning = ref(false)
let intervalId: number | null = null

// Format time as MM:SS
const formattedTime = computed(() => {
  const minutes = Math.floor(elapsedSeconds.value / 60)
  const seconds = elapsedSeconds.value % 60
  return `${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`
})

// Timer controls
function start() {
  if (!isRunning.value) {
    isRunning.value = true
    intervalId = window.setInterval(() => {
      elapsedSeconds.value++
    }, 1000)
  }
}

function pause() {
  if (isRunning.value && intervalId !== null) {
    isRunning.value = false
    clearInterval(intervalId)
    intervalId = null
  }
}

function reset() {
  pause()
  elapsedSeconds.value = 0
}

// Cleanup on unmount
onUnmounted(() => {
  if (intervalId !== null) {
    clearInterval(intervalId)
  }
})
</script>

<template>
  <div class="min-h-screen bg-white flex flex-col items-center justify-center">
    <!-- Timer Display -->
    <div class="mb-16">
      <span class="text-8xl font-extralight tracking-tight text-gray-900">
        {{ formattedTime }}
      </span>
    </div>

    <!-- Controls -->
    <div class="flex items-center gap-6">
      <!-- Start/Pause Button -->
      <button
        v-if="!isRunning"
        @click="start"
        class="w-16 h-16 rounded-full bg-gray-900 text-white flex items-center justify-center hover:bg-gray-700 transition-colors"
        aria-label="Iniciar"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6 ml-1" viewBox="0 0 24 24" fill="currentColor">
          <path d="M8 5v14l11-7z"/>
        </svg>
      </button>
      
      <button
        v-else
        @click="pause"
        class="w-16 h-16 rounded-full bg-gray-900 text-white flex items-center justify-center hover:bg-gray-700 transition-colors"
        aria-label="Pausar"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="w-6 h-6" viewBox="0 0 24 24" fill="currentColor">
          <path d="M6 4h4v16H6V4zm8 0h4v16h-4V4z"/>
        </svg>
      </button>

      <!-- Reset Button -->
      <button
        @click="reset"
        class="w-12 h-12 rounded-full bg-gray-100 text-gray-600 flex items-center justify-center hover:bg-gray-200 transition-colors"
        :class="{ 'opacity-50 cursor-not-allowed': elapsedSeconds === 0 }"
        :disabled="elapsedSeconds === 0"
        aria-label="Reiniciar"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="w-5 h-5" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
          <path d="M3 12a9 9 0 1 0 9-9 9.75 9.75 0 0 0-6.74 2.74L3 8"/>
          <path d="M3 3v5h5"/>
        </svg>
      </button>
    </div>

    <!-- App Title -->
    <p class="mt-20 text-sm text-gray-400 tracking-widest uppercase">Minutimer</p>
  </div>
</template>
