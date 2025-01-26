<template>
    <div class="flex flex-col h-full relative">
        <!-- Ввод имени -->
        <div v-if="step === 0" class="flex-grow flex items-center justify-center p-2">
            <div class="flex flex-col overflow-hidden w-full md:max-w-lg">
                <div class="flex-grow p-4 flex flex-col gap-2">
                    <h2 class="text-lg font-bold text-slate-700">Введите имена игроков</h2>
                    <div class="flex flex-col gap-3 w-full">
                        <input
                            v-model="firstPlayerName"
                            type="text"
                            placeholder="Первый игрок"
                            class="p-2 rounded w-full shadow text-lg text-slate-700"
                        />
                        <input
                            v-model="secondPlayerName"
                            type="text"
                            placeholder="Второй игрок"
                            class="p-2 rounded w-full shadow text-lg text-slate-700"
                        />
                    </div>
                </div>
                <div class="px-4 flex gap-4 justify-between">
                    <button
                        @click="nextStep"
                        class="bg-emerald-500 rounded shadow flex items-center justify-center p-2 text-white text-lg hover:bg-emerald-400 transition-colors w-3/12"
                    >
                        Далее
                    </button>
                    <button
                        @click="resetNames"
                        class="flex items-center justify-center p-2 text-red-500 text-lg hover:text-red-400 transition-colors w-4/12"
                    >
                        Сбросить имена
                    </button>
                </div>
            </div>
        </div>
        <!-- Выбор первого подающего игрока -->
        <div v-if="step === 1" class="flex-grow flex items-center justify-center p-2">
            <div class="flex flex-col overflow-hidden w-full md:max-w-lg">
                <div class="flex-grow p-4 flex flex-col gap-2">
                    <button
                        class="font-bold underline flex items-center w-fit text-slate-700 hover:text-slate-500 transition-colors"
                        @click="prevStep"
                    >
                        <svg
                            xmlns="http://www.w3.org/2000/svg"
                            width="24"
                            height="24"
                            viewBox="0 0 24 24"
                            fill="none"
                            stroke="currentColor"
                            stroke-width="2"
                            stroke-linecap="round"
                            stroke-linejoin="round"
                        >
                            <!-- <path d="M19 12H5" /> -->
                            <polyline points="12 19 5 12 12 5" />
                        </svg>
                        К вводу имен
                    </button>
                    <h2 class="text-lg font-bold text-slate-700">
                        Выберите первого подающего игрока
                    </h2>
                    <div class="flex flex-col gap-3 w-full">
                        <button
                            @click="startGame(1)"
                            class="bg-emerald-500 w-full rounded shadow flex items-center justify-center p-2 text-white text-lg hover:bg-emerald-400 transition-colors"
                        >
                            {{ firstPlayerName }}
                        </button>
                        <button
                            @click="startGame(2)"
                            class="bg-emerald-500 w-full rounded shadow flex items-center justify-center p-2 text-white text-lg hover:bg-emerald-400 transition-colors"
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
                    <h2 class="text-lg font-bold text-slate-700">
                        Победил: {{ winner === 1 ? firstPlayerName : secondPlayerName }}
                    </h2>
                    <div class="flex flex-col gap-3 w-full">
                        <button
                            @click="reset"
                            class="bg-emerald-500 w-full rounded shadow flex items-center justify-center p-2 text-white text-lg hover:bg-emerald-400 transition-colors"
                        >
                            Начать заново
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <!-- Кнопки управления -->
        <ControlButtons
            v-if="step === 2 && !gameOver"
            @changeSide="playerOrder = !playerOrder"
            @cancelTurn="cancelTurn"
            @resetGame="reset"
        />
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
const firstPlayerName = defineModel('firstPlayerName', { default: '' })
const secondPlayerName = defineModel('secondPlayerName', { default: '' })
const playerOrder = ref(false)

// История ходов теперь хранит только изменения
const history = ref([])

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
    history.value = []
}

function nextStep() {
    step.value++
    firstPlayerName.value = firstPlayerName.value || 'Первый игрок'
    secondPlayerName.value = secondPlayerName.value || 'Второй игрок'
}

function prevStep() {
    if (step.value > 0) {
        step.value--
    }
}

function startGame(player) {
    step.value++
    activePlayer.value = player
    gameStarted.value = true
    history.value.push({ action: 'start', player })
}

function handlePlayerClick(player) {
    incrementScore(player)
}

function incrementScore(player) {
    const prevActivePlayer = activePlayer.value

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

    // Сохраняем ход в историю
    history.value.push({
        action: 'score',
        player,
        prevActivePlayer,
        newActivePlayer: activePlayer.value,
    })
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
    history.value.push({ action: 'end', winner: winningPlayer })
}

function cancelTurn() {
    if (history.value.length > 0) {
        const lastAction = history.value.pop()

        if (lastAction.action === 'score') {
            if (lastAction.player === 1) {
                firstPlayerScore.value--
            } else {
                secondPlayerScore.value--
            }
            activePlayer.value = lastAction.prevActivePlayer
        } else if (lastAction.action === 'end') {
            gameOver.value = false
            gameStarted.value = true
            winner.value = null
            step.value = 2
        } else if (lastAction.action === 'start') {
            reset()
        }
    }
}

function resetNames() {
    firstPlayerName.value = ''
    secondPlayerName.value = ''
}
</script>
