<template>
    <div class="flex flex-col h-full">
        <div class="p-4 bg-slate-50">
            <button @click="reset" class="bg-slate-400 px-4 py-2 rounded text-white">
                Сбросить счет
            </button>
        </div>
        <div class="flex flex-col sm:flex-row flex-grow">
            <div class="flex-1 flex flex-col">
                <div class="text-center p-2 bg-emerald-200">Первый игрок</div>
                <button
                    @click="incrementScore(1)"
                    class="flex-1 text-9xl flex items-center justify-center w-full transition-colors duration-300"
                    :class="activePlayer === 1 ? 'bg-emerald-300' : 'bg-emerald-100'"
                >
                    {{ firstPlayerScore }}
                </button>
            </div>
            <div class="flex-1 flex flex-col">
                <div class="text-center p-2 bg-sky-200">Второй игрок</div>
                <button
                    @click="incrementScore(2)"
                    class="flex-1 text-9xl flex items-center justify-center w-full transition-colors duration-300"
                    :class="activePlayer === 2 ? 'bg-sky-300' : 'bg-sky-100'"
                >
                    {{ secondPlayerScore }}
                </button>
            </div>
        </div>
        <div v-if="gameStarted" class="text-center p-4 bg-slate-200 text-xl">
            Подает: {{ activePlayer === 1 ? 'Первый' : 'Второй' }} игрок
        </div>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const firstPlayerScore = ref(0)
const secondPlayerScore = ref(0)
const activePlayer = ref(null)
const gameStarted = ref(false)

const totalScore = computed(() => firstPlayerScore.value + secondPlayerScore.value)

function reset() {
    firstPlayerScore.value = 0
    secondPlayerScore.value = 0
    activePlayer.value = null
    gameStarted.value = false
}

function incrementScore(player) {
    if (!gameStarted.value) {
        gameStarted.value = true
        activePlayer.value = player
    } else {
        if (player === 1) {
            firstPlayerScore.value++
        } else {
            secondPlayerScore.value++
        }

        if (totalScore.value % 2 === 0) {
            activePlayer.value = activePlayer.value === 1 ? 2 : 1
        }
    }
}
</script>
