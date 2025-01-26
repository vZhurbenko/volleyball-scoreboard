<script setup>
import { defineEmits } from 'vue'
const emit = defineEmits(['changeSide', 'cancelTurn', 'resetGame'])
function changeSide() {
    emit('changeSide')
}
function cancelTurn() {
    emit('cancelTurn')
}
function resetGame() {
    emit('resetGame')
}
</script>

<template>
    <div
        class="fixed sm:top-0 top-[env(safe-area-inset-top)] left-1/2 transform -translate-x-1/2 bg-slate-100 shadow rounded overflow-hidden"
    >
        <button class="px-4 py-2 touch-manipulation text-slate-700" @click="cancelTurn">
            <svg width="24" height="24" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path
                    d="M12.5 8c-2.65 0-5.05.99-6.9 2.6L2 7v9h9l-3.62-3.62c1.39-1.16 3.16-1.88 5.12-1.88 3.54 0 6.55 2.31 7.6 5.5l2.37-.78C21.08 11.03 17.15 8 12.5 8z"
                    fill="currentColor"
                />
            </svg>
        </button>
        <button
            class="px-4 py-2 touch-manipulation rotate-90 sm:rotate-0 text-slate-700"
            @click="changeSide"
        >
            <svg width="24" height="24" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path
                    d="M21 9l-4-4v3H3v2h14v3l4-4zm-18 6l4 4v-3h14v-2H7v-3l-4 4z"
                    fill="currentColor"
                />
            </svg>
        </button>

        <button class="px-4 py-2 touch-manipulation text-slate-700" @click="resetGame">
            <svg width="24" height="24" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path
                    d="M17.65 6.35C16.2 4.9 14.21 4 12 4c-4.42 0-7.99 3.58-7.99 8s3.57 8 7.99 8c3.73 0 6.84-2.55 7.73-6h-2.08c-.82 2.33-3.04 4-5.65 4-3.31 0-6-2.69-6-6s2.69-6 6-6c1.66 0 3.14.69 4.22 1.78L13 11h7V4l-2.35 2.35z"
                    fill="currentColor"
                />
            </svg>
        </button>
    </div>
</template>
