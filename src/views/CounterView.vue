<template>
    <div class="flex flex-col h-full">
        <div class="px-4 pt-4">
            <button
                @click="reset"
                class="rounded font-bold uppercase text-sm p-2 bg-transparent focus-visible:outline focus-visible:outline-1 focus-visible:outline-offset-[1px] ease-linear transition-all duration-150 text-red-500 active:text-red-600 focus-visible:outline-red-500/50 border-2 border-red-500"
            >
                Сбросить счет
            </button>
        </div>
        <div class="flex flex-col sm:flex-row flex-grow p-4 gap-4">
            <div class="flex-1 flex flex-col shadow-md rounded overflow-hidden">
                <div
                    class="text-center p-2 min-h-10"
                    :class="activePlayer === 1 ? 'bg-emerald-500' : 'bg-slate-200'"
                ></div>
                <button
                    @click="handlePlayerClick(1)"
                    class="flex-1 text-9xl flex items-center justify-center w-full transition-colors duration-300 bg-slate-200"
                >
                    {{ firstPlayerScore }}
                </button>
            </div>
            <div class="flex-1 flex flex-col shadow-md rounded overflow-hidden">
                <div
                    class="text-center p-2 min-h-10"
                    :class="activePlayer === 2 ? 'bg-red-500' : 'bg-slate-200'"
                ></div>
                <button
                    @click="handlePlayerClick(2)"
                    class="flex-1 text-9xl flex items-center justify-center w-full transition-colors duration-300 bg-slate-200"
                >
                    {{ secondPlayerScore }}
                </button>
            </div>
        </div>
        <div v-if="gameStarted && !gameOver" class="text-center p-4 bg-slate-200 text-xl">
            Подает: {{ activePlayer === 1 ? 'Первый' : 'Второй' }} игрок
        </div>
        <div v-if="gameOver" class="text-center p-4 bg-yellow-200 text-xl font-bold">
            Игра окончена! Победил {{ winner === 1 ? 'Первый' : 'Второй' }} игрок! Нажмите на кнопку
            игрока, чтобы начать новую игру.
        </div>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const firstPlayerScore = ref(0)
const secondPlayerScore = ref(0)
const activePlayer = ref(null)
const gameStarted = ref(false)
const gameOver = ref(false)
const winner = ref(null)

const totalScore = computed(() => firstPlayerScore.value + secondPlayerScore.value)
const isDeuce = computed(() => firstPlayerScore.value >= 10 && secondPlayerScore.value >= 10)

function reset() {
    firstPlayerScore.value = 0
    secondPlayerScore.value = 0
    activePlayer.value = null
    gameStarted.value = false
    gameOver.value = false
    winner.value = null
}

function handlePlayerClick(player) {
    if (gameOver.value) {
        reset()
        gameStarted.value = true
        activePlayer.value = player
    } else {
        incrementScore(player)
    }
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

        checkGameEnd()

        if (!gameOver.value) {
            if (isDeuce.value) {
                activePlayer.value = activePlayer.value === 1 ? 2 : 1
            } else if (totalScore.value % 2 === 0) {
                activePlayer.value = activePlayer.value === 1 ? 2 : 1
            }
        }
    }
}

function checkGameEnd() {
    if (isDeuce.value) {
        if (Math.abs(firstPlayerScore.value - secondPlayerScore.value) === 2) {
            endGame(firstPlayerScore.value > secondPlayerScore.value ? 1 : 2)
        }
    } else {
        if (firstPlayerScore.value >= 11) {
            endGame(1)
        } else if (secondPlayerScore.value >= 11) {
            endGame(2)
        }
    }
}

function endGame(winningPlayer) {
    gameOver.value = true
    winner.value = winningPlayer
}
</script>
