<template>
    <div class="flex flex-col h-full px-4">
        <div class="flex flex-col sm:flex-row flex-grow py-4 gap-4">
            <div
                class="flex-1 flex flex-col shadow-md rounded overflow-hidden transition-colors duration-100"
            >
                <div
                    class="text-center min-h-10"
                    :class="activePlayer === 1 ? 'bg-emerald-500' : 'bg-slate-200'"
                ></div>
                <button
                    @click="handlePlayerClick(1)"
                    class="flex-1 text-9xl flex items-center justify-center w-full transition-colors duration-100 bg-slate-200"
                    :aria-label="`Первый игрок: ${firstPlayerScore}`"
                    :class="activePlayer === 1 ? 'text-emerald-500' : 'text-black'"
                >
                    {{ firstPlayerScore }}
                </button>
            </div>
            <div
                class="flex-1 flex flex-col shadow-md rounded overflow-hidden transition-colors duration-100"
            >
                <div
                    class="text-center min-h-10"
                    :class="activePlayer === 2 ? 'bg-red-500' : 'bg-slate-200'"
                ></div>
                <button
                    @click="handlePlayerClick(2)"
                    class="flex-1 text-9xl flex items-center justify-center w-full transition-colors duration-100 bg-slate-200"
                    :aria-label="`Второй игрок: ${secondPlayerScore}`"
                    :class="activePlayer === 2 ? 'text-red-500' : 'text-black'"
                >
                    {{ secondPlayerScore }}
                </button>
            </div>
        </div>
        <div
            class="flex items-center justify-between p-4 bg-slate-200 rounded-t overflow-hidden shadow-md"
        >
            <div class="text-center font-bold uppercase text-sm">{{ statusText }}</div>
            <div>
                <button
                    @click="reset"
                    class="font-bold uppercase text-sm p-2 focus-visible:outline focus-visible:outline-1 focus-visible:outline-offset-[1px] ease-linear transition-all duration-150 text-red-500 active:text-red-600 focus-visible:outline-red-500/50"
                >
                    Сбросить счет
                </button>
            </div>
        </div>
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const WINNING_SCORE = 11
const DEUCE_SCORE = 10
const DEUCE_DIFFERENCE = 2

const firstPlayerScore = ref(0)
const secondPlayerScore = ref(0)
const activePlayer = ref(null)
const gameStarted = ref(false)
const gameOver = ref(false)
const winner = ref(null)

const totalScore = computed(() => firstPlayerScore.value + secondPlayerScore.value)
const isDeuce = computed(
    () => firstPlayerScore.value >= DEUCE_SCORE && secondPlayerScore.value >= DEUCE_SCORE,
)

const statusText = computed(() => {
    if (!gameStarted.value) {
        return 'Для начала игры выберите подающего игрока'
    }
    if (gameOver.value) {
        return `Игра окончена! Победил ${winner.value === 1 ? 'Первый' : 'Второй'} игрок! Нажмите на кнопку игрока, чтобы начать новую игру.`
    }
    return `Подает: ${activePlayer.value === 1 ? 'Первый' : 'Второй'} игрок`
})

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
        return
    }

    if (!gameStarted.value) {
        gameStarted.value = true
        activePlayer.value = player
        return
    }

    incrementScore(player)
}

function incrementScore(player) {
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

function checkGameEnd() {
    if (isDeuce.value) {
        if (Math.abs(firstPlayerScore.value - secondPlayerScore.value) === DEUCE_DIFFERENCE) {
            endGame(firstPlayerScore.value > secondPlayerScore.value ? 1 : 2)
        }
    } else {
        if (firstPlayerScore.value >= WINNING_SCORE) {
            endGame(1)
        } else if (secondPlayerScore.value >= WINNING_SCORE) {
            endGame(2)
        }
    }
}

function endGame(winningPlayer) {
    gameOver.value = true
    winner.value = winningPlayer
}
</script>
