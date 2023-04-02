<script setup lang="ts">
/**
 * Keeping it in a single file similar to coderbyte assessment
 */
import { computed, ref, watchEffect } from 'vue';
const currentPlayer = ref<String>("X")
const board = ref<String[]>(Array(9).fill(''))

const winPatterns = [
    // has to be sorted
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 4, 8],
    [1, 4, 7],
    [2, 4, 6],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
]

const winner = ref<string | null>(null)
const gameOver = ref<boolean>(false)
const playerXMoves = ref<number[]>([])
const playerOMoves = ref<number[]>([])

const message = computed(() => {
    if (gameOver.value) {
        if (winner.value) return winner.value + ' has won'
        return
    }
    else {
        return `Next player: ${currentPlayer.value}`
    }
})

const resetBoard = () => {
    gameOver.value = false;
    winner.value = null
    board.value = Array(9).fill('')
    currentPlayer.value = 'X'
    playerOMoves.value = []
    playerXMoves.value = []
}

const handleClick = (idx: number) => {
    if (!board.value[idx] && !gameOver.value && !winner.value) {

        board.value[idx] = currentPlayer.value

        if (currentPlayer.value === 'X') { playerXMoves.value.push(idx) }
        else { playerOMoves.value.push(idx) }

        currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'
    }
}

const checkForPatternMatch = (arrOfArray: number[][], target: number[]) => {
    const sorted = target.sort((a, b) => a - b)
    return arrOfArray.some(pattern => pattern.every(t => sorted.includes(t)))
}

watchEffect(() => {

    if (checkForPatternMatch(winPatterns, playerXMoves.value)) {
        winner.value = 'X', gameOver.value = true;
    }

    if (checkForPatternMatch(winPatterns, playerOMoves.value)) {
        winner.value = 'O', gameOver.value = true;
    }

    if (board.value.every(val => val !== '')) gameOver.value = true;
})


</script>

<template>
    <div class="p-4 select-none">
        <p>{{ message }}</p>
        <button type="reset" @click="resetBoard" class="rounded-md hover:bg-gray-200 my-2 px-4 py-1 border">Reset</button>
        <div class="grid grid-cols-3 max-w-fit gap-0">
            <button type="button" @click="handleClick(idx)
            " v-for="(square, idx) in board" :key="idx" class=" hover:bg-gray-200 w-10 h-10 border border-gray-800">
                {{ square }}
            </button>
        </div>

    </div>
</template>

