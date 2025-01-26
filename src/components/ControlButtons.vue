<script setup>
import { defineEmits } from 'vue'
const emit = defineEmits(['changeSide', 'cancelTurn'])
function changeSide() {
    emit('changeSide')
}
function cancelTurn() {
    emit('cancelTurn')
}
</script>

<template>
    <div
        class="fixed sm:top-0 top-[env(safe-area-inset-top)] left-1/2 transform -translate-x-1/2 bg-slate-100 shadow rounded overflow-hidden"
    >
        <button class="px-4 py-2 touch-manipulation text-slate-700">
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

        <!-- <button class="px-4 py-2 touch-manipulation">
            <svg width="24" height="24" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
                <path
                    d="M19.14 12.94c.04-.3.06-.61.06-.94 0-.32-.02-.64-.07-.94l2.03-1.58c.18-.14.23-.41.12-.61l-1.92-3.32c-.12-.22-.37-.29-.59-.22l-2.39.96c-.5-.38-1.03-.7-1.62-.94l-.36-2.54c-.04-.24-.24-.41-.48-.41h-3.84c-.24 0-.43.17-.47.41l-.36 2.54c-.59.24-1.13.57-1.62.94l-2.39-.96c-.22-.08-.47 0-.59.22L2.74 8.87c-.12.21-.08.47.12.61l2.03 1.58c-.05.3-.09.63-.09.94s.02.64.07.94l-2.03 1.58c-.18.14-.23.41-.12.61l1.92 3.32c.12.22.37.29.59.22l2.39-.96c.5.38 1.03.7 1.62.94l.36 2.54c.05.24.24.41.48.41h3.84c.24 0 .44-.17.47-.41l.36-2.54c.59-.24 1.13-.56 1.62-.94l2.39.96c.22.08.47 0 .59-.22l1.92-3.32c.12-.22.07-.47-.12-.61l-2.01-1.58zM12 15.6c-1.98 0-3.6-1.62-3.6-3.6s1.62-3.6 3.6-3.6 3.6 1.62 3.6 3.6-1.62 3.6-3.6 3.6z"
                    fill="currentColor"
                />
            </svg>
        </button> -->
    </div>
</template>
