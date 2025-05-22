<script setup>
import { ref, computed, onActivated } from "vue";

const player = ref("X");
const board = ref([
  ["", "", ""],
  ["", "", ""],
  ["", "", ""],
]);
const calculateWinner = (squares) => {
  const lines = [
    [0, 1, 2],
    [3, 4, 5],
    [6, 7, 8],
    [0, 3, 6],
    [1, 4, 7],
    [2, 5, 8],
    [0, 4, 8],
    [2, 4, 6],
  ];
  for (let i = 0; i < lines.length; i++) {
    const [a, b, c] = lines[i];
    if (squares[a] && squares[a] === squares[b] && squares[a] === squares[c]) {
      return squares[a];
    }
  }
  return null;
};
const winner = computed(() => calculateWinner(board.value.flat()));
const isSinglePlayer = ref(true);
const makeAIMove = () => {
  if (winner.value) return;

  const emptyCells = [];
  for (let i = 0; i < 3; i++) {
    for (let j = 0; j < 3; j++) {
      if (board.value[i][j] === "") {
        emptyCells.push([i, j]);
      }
    }
  }
  if (emptyCells.length === 0) return;
  const [x, y] = emptyCells[Math.floor(Math.random() * emptyCells.length)];
  board.value[x][y] = player.value;
  player.value = "X";
};

const makeMove = (x, y) => {
  if (winner.value || board.value[x][y] !== "") return;

  board.value[x][y] = player.value;

  if (isSinglePlayer.value && player.value === "X") {
    player.value = "O";
    setTimeout(() => {
      makeAIMove();
    }, 300);
  } else {
    player.value = player.value === "X" ? "O" : "X";
  }
};

const toggleMode = () => {
  isSinglePlayer.value = !isSinglePlayer.value;
  resetGame();
};

const resetGame = () => {
  board.value = [
    ["", "", ""],
    ["", "", ""],
    ["", "", ""],
  ];
  player.value = "X";
};
</script>

<template>
  <main
    class="pt-8 text-center dark: bg-gray-800 min-h-screen dark: text-white"
  >
    <h1 class="mb-8 text-3xl font-bold uppercase">Tic Tac Toe</h1>
    <div class="mb-4">
      <button
        @click="toggleMode"
        class="px-4 py-2 bg-blue-500 rounded hover:bg-blue-700 duration-300"
      >
        {{
          isSinglePlayer ? "Switch to 2 Player Mode" : "Switch to 1 Player Mode"
        }}
      </button>
    </div>

    <h3 class="text-xl mb-4">
      {{ isSinglePlayer ? "Single Player Mode" : "Two Player Mode" }} - Player
      {{ player }}'s turn
    </h3>

    <div class="flex flex-col items-center mb-8">
      <div v-for="(row, x) in board" :key="x" class="flex">
        <div
          v-for="(cell, y) in row"
          :key="y"
          @click="makeMove(x, y)"
          :class="`border border-white w-20 h-20 hover:bg-gray-700 flex items-center justify-center material-icons-outlined text-4xl cursor-pointer ${
            cell === 'X' ? 'text-pink-500' : 'text-blue-500'
          }`"
        >
          {{ cell === "X" ? "X" : cell === "O" ? "O" : "" }}
        </div>
      </div>
    </div>
    <h2 v-if="winner" class="text-6xl mb-8 font-bold">
      Player {{ winner }} wins!
    </h2>
    <button
      @click="resetGame"
      class="px-4 py-2 bg-pink-500 rounded uppercase hover:bg-pink-800 cursor-pointer duration-300"
    >
      Reset Game
    </button>
  </main>
</template>
