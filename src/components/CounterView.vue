<template>
    <div class="flex flex-col h-full relative">
        <!-- Ввод имени -->
        <div v-if="step === 0" class="flex-grow flex items-center justify-center p-2">
            <div class="flex flex-col overflow-hidden w-full md:max-w-lg">
                <div class="flex-grow p-4 flex flex-col gap-2">
                    <h2 class="text-lg font-bold">Введите имена игроков</h2>
                    <div class="flex flex-col gap-3 w-full">
                        <input
                            v-model="firstPlayerName"
                            type="text"
                            placeholder="Первый игрок"
                            class="p-2 rounded w-full shadow text-lg"
                        />
                        <input
                            v-model="secondPlayerName"
                            type="text"
                            placeholder="Второй игрок"
                            class="p-2 rounded w-full shadow text-lg"
                        />
                    </div>
                </div>
                <div class="px-4">
                    <button
                        @click="nextStep"
                        class="bg-emerald-500 w-full rounded shadow flex items-center justify-center p-2 text-white text-lg"
                    >
                        Далее
                    </button>
                </div>
            </div>
        </div>
        <!-- Выбор первого подающего игрока -->
        <div v-if="step === 1" class="flex-grow flex items-center justify-center p-2">
            <div class="flex flex-col overflow-hidden w-full md:max-w-lg">
                <div class="flex-grow p-4 flex flex-col gap-2">
                    <button class="font-bold underline flex items-center w-fit" @click="prevStep">
                        <img src="../assets/img/backArrow.png" class="h-[20px]" />Назад
                    </button>
                    <h2 class="text-lg font-bold">Выберите первого подающего игрока</h2>
                    <div class="flex flex-col gap-3 w-full">
                        <button
                            @click="startGame(1)"
                            class="bg-emerald-500 w-full rounded shadow flex items-center justify-center p-2 text-white text-lg"
                        >
                            {{ firstPlayerName }}
                        </button>
                        <button
                            @click="startGame(2)"
                            class="bg-emerald-500 w-full rounded shadow flex items-center justify-center p-2 text-white text-lg"
                        >
                            {{ secondPlayerName }}
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <!-- Табло -->
        <div
            v-else-if="step === 2"
            class="flex flex-col sm:flex-row flex-grow p-4 gap-4 w-full"
            :class="
                !playerOrder
                    ? ['flex-col', 'sm:flex-row']
                    : ['flex-col-reverse', 'sm:flex-row-reverse']
            "
        >
            <div
                class="flex-1 flex flex-col shadow rounded overflow-hidden transition-colors duration-100"
            >
                <button
                    @click="handlePlayerClick(1)"
                    class="flex-1 font-bold text-[35cqmin] flex items-center justify-center w-full transition-colors duration-100 bg-white touch-manipulation"
                    :aria-label="`Первый игрок: ${firstPlayerScore}`"
                    :class="activePlayer === 1 ? 'text-blue-500' : 'text-slate-700'"
                >
                    {{ firstPlayerScore }}
                </button>
                <div
                    class="text-center min-h-10 flex items-center justify-center text-lg"
                    :class="
                        activePlayer === 1
                            ? ['bg-blue-500', 'text-white']
                            : ['bg-white', 'text-slate-700']
                    "
                >
                    {{ firstPlayerName }}
                </div>
            </div>
            <div
                class="flex-1 flex flex-col shadow rounded overflow-hidden transition-colors duration-100"
            >
                <button
                    @click="handlePlayerClick(2)"
                    class="flex-1 font-bold text-[35cqmin] flex items-center justify-center w-full transition-colors duration-100 bg-white touch-manipulation"
                    :aria-label="`Второй игрок: ${secondPlayerScore}`"
                    :class="activePlayer === 2 ? 'text-red-500' : 'text-slate-700'"
                >
                    {{ secondPlayerScore }}
                </button>
                <div
                    class="text-center min-h-10 flex items-center justify-center text-lg"
                    :class="
                        activePlayer === 2
                            ? ['bg-red-500', 'text-white']
                            : ['bg-white', 'text-slate-700']
                    "
                >
                    {{ secondPlayerName }}
                </div>
            </div>
        </div>
        <div v-if="gameOver" class="flex-grow flex items-center justify-center p-2">
            <div class="flex flex-col overflow-hidden w-full md:max-w-lg">
                <div class="flex-grow p-4 flex flex-col gap-2">
                    <h2 class="text-lg font-bold">
                        Победил: {{ winner === 1 ? firstPlayerName : secondPlayerName }}
                    </h2>
                    <div class="flex flex-col gap-3 w-full">
                        <button
                            @click="reset"
                            class="bg-emerald-500 w-full rounded shadow flex items-center justify-center p-2 text-white text-lg"
                        >
                            Начать заново
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <!-- Кнопки управления -->
        <ControlButtons v-if="step === 2 && !gameOver" @changeSide="playerOrder = !playerOrder" />
    </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import ControlButtons from './ControlButtons.vue'

const WINNING_SCORE = 11
const DEUCE_SCORE = 10
const DEUCE_DIFFERENCE = 2
const step = ref(0)
const firstPlayerScore = ref(0)
const secondPlayerScore = ref(0)
const activePlayer = ref(null)
const gameStarted = ref(false)
const gameOver = ref(false)
const winner = ref(null)
const firstPlayerName = defineModel('firstPlayerName', { default: 'Первый игрок' })
const secondPlayerName = defineModel('secondPlayerName', { default: 'Второй игрок' })
const playerOrder = ref(false)

const totalScore = computed(() => firstPlayerScore.value + secondPlayerScore.value)
const isDeuce = computed(
    () => firstPlayerScore.value >= DEUCE_SCORE && secondPlayerScore.value >= DEUCE_SCORE,
)

function reset() {
    firstPlayerScore.value = 0
    secondPlayerScore.value = 0
    activePlayer.value = null
    gameStarted.value = false
    gameOver.value = false
    winner.value = null
    step.value = 0
    playerOrder.value = false
}
function nextStep() {
    step.value++
}
function prevStep() {
    if (step.value > 0) {
        step.value--
    }
}
function startGame(player) {
    step.value++
    activePlayer.value = player
}

function handlePlayerClick(player) {
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
    gameStarted.value = false
    winner.value = winningPlayer
    step.value = 3
}
</script>
