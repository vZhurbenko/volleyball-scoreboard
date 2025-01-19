<template>
    <div class="app-container safe-area-inset">
        <RouterView />
    </div>
</template>

<style>
html,
body {
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden;
}

.app-container {
    height: 100vh; /* Fallback for browsers that do not support Custom Properties */
    height: calc(var(--vh, 1vh) * 100);
    overflow-y: auto;
    -webkit-overflow-scrolling: touch;
    display: flex;
    flex-direction: column;
}

.safe-area-inset {
    padding-top: env(safe-area-inset-top);
    padding-right: env(safe-area-inset-right);
    padding-bottom: env(safe-area-inset-bottom);
    padding-left: env(safe-area-inset-left);
}
</style>

<script setup>
import { onMounted, onUnmounted } from 'vue'

const setViewportHeight = () => {
    let vh = window.innerHeight * 0.01
    document.documentElement.style.setProperty('--vh', `${vh}px`)
}

onMounted(() => {
    setViewportHeight()
    window.addEventListener('resize', setViewportHeight)
})

onUnmounted(() => {
    window.removeEventListener('resize', setViewportHeight)
})
</script>
