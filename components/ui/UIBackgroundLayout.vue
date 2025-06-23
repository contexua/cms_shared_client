<script setup>
import { useBackground } from '@/composables/navigation/useBackground';
const { backgroundStyle } = useBackground();
</script>

<template>
  <div class="relative w-full h-screen" :style="backgroundStyle">
    <!-- Overlay -->
    <div class="absolute inset-0 bg-black/40" />

    <!-- Content Layer -->
    <div class="relative z-10 flex items-center justify-center h-full">
      <slot />
    </div>
  </div>
</template>
