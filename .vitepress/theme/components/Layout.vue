<template>
  <Layout />
  <svg style="position: fixed; right: 0%; bottom: 0%;transform: scale(0.5);transform-origin: 100%;" width="316" height="33" viewBox="0 0 316 33" fill="none" xmlns="http://www.w3.org/2000/svg">
    <path style="stroke-dasharray: 379 379; stroke-dashoffset: var(--offset, -379); transition: stroke-dashoffset 100ms ease-in-out;" d="M3 16.8466C18.4623 32.625 26.617 30.0853 41.75 16.8466C58.6017 1.37486 61.5855 -1.875 80.5 16.8466C94.7835 30.9844 103.179 32.4773 119.25 16.8466C134.383 -0.81124 142.867 0.117534 158 16.8466C173.132 32.785 181.618 30.7914 196.75 16.8466C211.079 0.722875 219.337 -3.81504 235.5 16.8466C248.83 34.6111 257.361 34.1564 274.25 16.8466C291.225 -1.64123 299.591 -0.443631 313 16.8466" stroke="url(#paint0_linear_35_20)" stroke-width="5" stroke-linecap="round"/>
    <defs>
      <linearGradient id="paint0_linear_35_20" x1="139.5" y1="3" x2="139.5" y2="36" gradientUnits="userSpaceOnUse">
        <stop stop-color="#3EAF7C"/>
        <stop offset="0.505" stop-color="#52E4A2"/>
        <stop offset="1" stop-color="#3EAF7C"/>
      </linearGradient>
    </defs>
</svg>
</template>

<script setup>
import confetti from 'canvas-confetti'
import DefaultTheme from 'vitepress/theme'
import useFancybox from '@hooks/useFancybox.ts'
import { onMounted, ref, watch } from 'vue';
import { useRoute } from 'vitepress'
useFancybox()

const { Layout } = DefaultTheme

const route = useRoute()
const isBound = ref(false)

watch(() => route.path, (n, o) => {
  if (n !== o) {
    isBound.value = false
  }
})

onMounted(() => {
  document.addEventListener('scroll', () => {
    const percentage = (window.scrollY / (document.body.scrollHeight - window.innerHeight)).toFixed(2);
    document.documentElement.style.setProperty("--offset", `${-379 + (percentage * 379)}`);
    if (percentage == 1 && !isBound.value) {
      isBound.value = true
      confetti({
        angle: 130,
        particleCount: 50,
        spread: 70,
        origin: { x: 1, y: 1 }
      })
    }
  })
})
</script>
