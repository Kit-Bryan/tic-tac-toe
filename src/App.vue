<script setup>
import { ref, computed } from "vue";
import confettiModule from "canvas-confetti";

const currentPlayer = ref('X')
const isDraw = ref(false)
// @formatter:off
const board = ref([
    ['','',''],
    ['','',''],
    ['','','']
])

const CalculateWinner = (board) => {
    const winningConditions = [
        [0,1,2],
        [3,4,5],
        [6,7,8],
        [0,3,6],
        [1,4,7],
        [2,5,8],
        [0,4,8],
        [2,4,6]
    ];
    // @formatter:on
    for (const condition of winningConditions) {
        const [a, b, c] = condition
        if (board[a] && board[a] === board[b] && board[a] === board[c]) {
            // We have a winner
            startConfetti();
            return board[a]
        }
    }

    // Check if all boxes are full
    isDraw.value = board.every(item => item !== "")
}

const winner = computed(() => CalculateWinner(board.value.flat()))

const makeMove = (y, x) => {
    if (winner.value) return;

    if (board.value[y][x] !== "") return;

    board.value[y][x] = currentPlayer.value;

    currentPlayer.value = currentPlayer.value === "X" ? "O" : "X";
}
const ResetGame = () => {
    // @formatter:off
    board.value = [
        ['','',''],
        ['','',''],
        ['','','']
    ]
    // @formatter:on
    currentPlayer.value = 'X'
}

async function startConfetti() {
    confettiModule({
        particleCount: 40,
        angle: 60,
        spread: 55,
        origin: {x: 0}
    })
    confettiModule({
        particleCount: 40,
        angle: 120,
        spread: 55,
        origin: {x: 1}
    })
}
</script>

<template>
    <main class="min-h-screen pt-8 text-center dark:bg-gray-800 dark:text-white">
        <h1 class="mb-8 text-3xl font-bold uppercase">Tic Tac Toe</h1>
        <h3 class="mb-4 text-xl" v-if="!winner && !isDraw">Player {{ currentPlayer }}'s turn</h3>
        <h3 class="mb-4 text-xl" v-else-if="winner">We have a winner!</h3>
        <h3 class="mb-4 text-xl" v-else-if="isDraw">Its a draw!</h3>

        <div class="mb-8 flex flex-col items-center">
            <div v-for="(row, y) in board"
                 :key="y"
                 class="flex"
            >
                <div v-for="(cell,x) in row"
                     :key="x"
                     @click="makeMove(y,x)"
                     :class="`border border-white w-24 h-24 hover:bg-gray-700
                     flex items-center justify-center
                     material-symbols-outlined
                     text-6xl
                     cursor-pointer ${cell === 'X' ? 'text-pink-500' : 'text-blue-500'}`"
                >
                    {{ cell === 'X' ? 'close' : cell === 'O' ? 'circle' : '' }}
                </div>
            </div>
        </div>
        <h2 v-if="winner"
            class="mb-8 text-6xl font-bold">Player {{ winner }} wins!
        </h2>
        <h2 v-else-if="isDraw" class="mb-8 text-6xl font-bold">Draw!</h2>
        <button @click="ResetGame" class="px-4 py-2 bg-pink-500 rounded uppercase
            font-bold hover:bg-pink-600 duration-300">Reset Game
        </button>

    </main>
</template>

<style>

</style>