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
                        class="bg-emerald-500 rounded shadow flex items-center justify-center p-2 text-white text-lg hover:bg-emerald-400 transition-colors w-4/12"
                    >
                        Далее
                    </button>
                    <button
                        @click="resetNames"
                        class="flex items-center justify-end p-2 text-red-500 text-lg hover:text-red-400 transition-colors w-6/12"
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

        <!-- Экран статистики -->
        <div v-if="gameOver" class="flex-grow flex items-center justify-center p-2">
            <div class="flex flex-col w-full sm:max-w-lg">
                <div class="flex-grow flex flex-col gap-4">
                    <!-- Статистика игры -->
                    <div class="bg-white p-2 rounded overflow-hidden shadow">
                        <h2 class="text-lg text-slate-700 px-2 pt-2 font-bold text-center">
                            Победил {{ winner === 1 ? firstPlayerName : secondPlayerName }} со
                            счетом
                        </h2>
                        <div class="text-lg text-slate-700 px-2 font-bold text-center">
                            <span :class="winner === 1 ? 'text-blue-500' : 'text-red-500'">
                                {{ winner === 1 ? firstPlayerScore : secondPlayerScore }}
                            </span>
                            :
                            <span :class="winner === 2 ? 'text-blue-500' : 'text-red-500'">
                                {{ winner === 2 ? firstPlayerScore : secondPlayerScore }}
                            </span>
                        </div>
                        <h3 class="text-lg text-slate-700 w-full text-center">
                            Статистика партии:
                        </h3>
                        <div
                            class="flex text-slate-700"
                            :class="winner === 1 ? 'flex-row' : 'flex-row-reverse'"
                        >
                            <!-- <div class="w-1/3 text-center py-1">№ Подачи</div> -->
                            <div class="w-1/2 text-center py-1 text-lg">
                                {{ firstPlayerName }}
                            </div>
                            <div class="w-1/2 text-center py-1 text-lg">
                                {{ secondPlayerName }}
                            </div>
                        </div>
                        <div class="overflow-y-auto max-h-80 divide-solid divide-y">
                            <div v-for="(action, index) in gameHistory" :key="index" class="flex">
                                <div
                                    class="flex w-full divide-solid"
                                    :class="winner === 1 ? 'flex-row' : 'flex-row-reverse'"
                                >
                                    <div
                                        class="w-1/2 text-center text-lg text-blue-500 flex justify-center items-center"
                                    >
                                        {{ action.player === 1 ? action.scoreAfter : '' }}
                                    </div>
                                    <div
                                        class="w-1/2 text-center text-lg text-red-500 flex justify-center items-center"
                                    >
                                        {{ action.player === 2 ? action.scoreAfter : '' }}
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <div class="flex flex-col gap-4 w-full">
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

        <!-- Кнопки управления табло-->
        <ControlButtons
            v-if="step === 2 && !gameOver"
            @changeSide="playerOrder = !playerOrder"
            @cancelTurn="cancelTurn"
            @resetGame="resetCurrentSet"
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
let serveNumber = ref(1)
let pointsOnCurrentServe = ref(0)

const history = ref([])

const totalScore = computed(() => firstPlayerScore.value + secondPlayerScore.value)
const isDeuce = computed(
    () => firstPlayerScore.value >= DEUCE_SCORE && secondPlayerScore.value >= DEUCE_SCORE,
)
const gameHistory = computed(() => {
    return history.value.filter((action) => action.action === 'score')
})

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
    serveNumber.value = 1
    pointsOnCurrentServe.value = 0
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
    serveNumber.value = 1
    pointsOnCurrentServe.value = 0
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

    const scoreAfter = player === 1 ? firstPlayerScore.value : secondPlayerScore.value

    // Сохраняем ход в историю
    history.value.push({
        action: 'score',
        player,
        prevActivePlayer,
        newActivePlayer: activePlayer.value,
        serveNumber: serveNumber.value,
        scoreAfter,
    })

    checkGameEnd()

    if (!gameOver.value) {
        serveNumber.value++
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
function resetCurrentSet() {
    if (history.value.length > 0) {
        firstPlayerScore.value = 0
        secondPlayerScore.value = 0
        activePlayer.value = history.value[0].player
        history.value = []
        history.value.push({ action: 'start', player: activePlayer.value })
        serveNumber.value = 1
        pointsOnCurrentServe.value = 0
    }
}
</script>
