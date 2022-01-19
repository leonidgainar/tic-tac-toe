<template>
  <div class="text-center">
    <h1 class="text-4xl text-center">Tic-Tac-Toe Game</h1>
    <div class="text-2xl pt-5">Match #{{ matchCounter }}</div>
    <div class="flex justify-center text-center">
      <div>
        <h2 class="text-xl pt-5">Who starts the next game?</h2>
        <div class="form-check pt-5">
          <input
            class="form-check-input appearance-none rounded-full h-4 w-4 border border-gray-300 bg-white checked:bg-blue-600 checked:border-blue-600 focus:outline-none align-top mt-1 mr-2 cursor-pointer"
            type="radio"
            v-model="selectedPlayer"
            value="O"
            id="playerO"
          />
          <label
            class="form-check-label inline-block text-gray-800"
            for="playerO"
          >
            You (Player O)
          </label>
        </div>
        <div class="form-check pt-3 pb-5">
          <input
            class="form-check-input appearance-none rounded-full h-4 w-4 border border-gray-300 bg-white checked:bg-red-600 checked:border-red-600 focus:outline-none align-top mt-1 mr-2 cursor-pointer"
            type="radio"
            v-model="selectedPlayer"
            value="X"
            id="playerX"
          />
          <label
            class="form-check-label inline-block text-gray-800"
            for="playerX"
          >
            Bot (Player X)
          </label>
        </div>
      </div>
    </div>
    <div class="min-w-full">
      <div class="bg-blue-200 shadow-xl rounded-lg">
        <div class="grid grid-cols-3 gap-1 md:gap-2 text-4xl md:text-6xl p-5">
          <GameBoardField
            v-for="n in 9"
            :key="n"
            :playerSymbol="setPlayerSymbol(n)"
            :textColor="setTextColor(n)"
            @field-clicked="playerNextMove(n)"
          />
        </div>
      </div>
      <div class="text-2xl py-10">
        <span class="text-blue-700">Player O</span>
        <span class="text-4xl px-4">
          {{ playerOWinsCount }} : {{ playerXWinsCount }}
        </span>
        <span class="text-red-700">Player X</span>
      </div>
    </div>
    <GameOverDialog
      :gameIsOver="gameIsOver"
      :gameResult="gameResult"
      @restart-game="restartGame"
    />
  </div>
</template>

<script>
import GameBoardField from "./GameBoardField.vue";
import GameOverDialog from "./GameOverDialog.vue";

export default {
  components: {
    GameBoardField,
    GameOverDialog
  },

  data() {
    return {
      winningCombinations: [
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9],
        [1, 4, 7],
        [2, 5, 8],
        [3, 6, 9],
        [1, 5, 9],
        [3, 5, 7]
      ],
      currentPlayer: "O",
      playerOMoves: [],
      playerXMoves: [],
      winningCombination: [],
      performedMoves: [],
      gameResult: "It's a DRAW!",
      matchCounter: 1,
      playerOWinsCount: 0,
      playerXWinsCount: 0,
      selectedPlayer: "X"
    };
  },

  computed: {
    gameIsOver() {
      return (
        this.winningCombination.length > 0 || this.performedMoves.length === 9
      );
    }
  },

  mounted() {
    this.botNextMove();
  },

  methods: {
    setPlayerSymbol(field) {
      if (this.playerOMoves.includes(field)) {
        return "O";
      }
      if (this.playerXMoves.includes(field)) {
        return "X";
      }
    },

    checkVictory(playerMoves) {
      let victory = false;
      this.winningCombinations.forEach((winCombination) => {
        let goodMoves = 0;
        playerMoves.forEach((move) => {
          if (winCombination.includes(move)) {
            goodMoves++;
          }
          if (goodMoves === 3) {
            victory = true;
            this.winningCombination = winCombination;
          }
        });
      });
      return victory;
    },

    getNextBestMoves(playerMoves) {
      let nextBestMoves = [];

      this.winningCombinations.forEach((winCombination) => {
        let goodMoves = 0;

        playerMoves.forEach((move) => {
          if (winCombination.includes(move)) {
            goodMoves++;
          }
          if (goodMoves === 2) {
            const remainingMovesForWin = winCombination.filter(
              (move) => !this.performedMoves.includes(move)
            );
            nextBestMoves.push(...remainingMovesForWin);
          }
        });
      });

      return [...new Set(nextBestMoves)];
    },

    getBotNextMove() {
      const playerNextBestMoves = this.getNextBestMoves(this.playerOMoves);
      const botNextBestMoves = this.getNextBestMoves(this.playerXMoves);

      let botNextMoves = [];

      if (botNextBestMoves.length) {
        botNextMoves = botNextBestMoves;
      } else if (playerNextBestMoves.length) {
        botNextMoves = playerNextBestMoves;
      } else {
        botNextMoves = [1, 2, 3, 4, 5, 6, 7, 8, 9].filter(
          (move) => !this.performedMoves.includes(move)
        );
      }

      return botNextMoves[Math.floor(Math.random() * botNextMoves.length)];
    },

    botNextMove() {
      setTimeout(() => {
        const bootMove = this.getBotNextMove();
        this.playerXMoves.push(bootMove);
        const playerXWins = this.checkVictory(this.playerXMoves);
        if (playerXWins) {
          this.gameResult = `Player ${this.currentPlayer} WINS!`;
          this.playerXWinsCount++;
        } else {
          this.currentPlayer = "O";
        }
        this.performedMoves.push(bootMove);
      }, 150);
    },

    playerNextMove(move) {
      this.playerOMoves.push(move);
      const playerOWins = this.checkVictory(this.playerOMoves);
      if (playerOWins) {
        this.gameResult = `Player ${this.currentPlayer} WINS!`;
        this.playerOWinsCount++;
      } else {
        this.currentPlayer = "X";
      }
      this.performedMoves.push(move);

      if (!playerOWins && this.performedMoves.length < 9) {
        this.botNextMove();
      }
    },

    restartGame() {
      this.matchCounter++;
      this.currentPlayer = "O";
      this.playerOMoves = [];
      this.playerXMoves = [];
      this.winningCombination = [];
      this.performedMoves = [];
      this.gameResult = "It's a DRAW!";
      if (this.selectedPlayer === "X") {
        this.botNextMove();
      }
    },

    setTextColor(field) {
      return this.winningCombination.includes(field)
        ? "text-black"
        : "text-white";
    }
  }
};
</script>
