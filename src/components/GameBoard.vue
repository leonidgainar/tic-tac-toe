<template>
  <div class="text-center">
    <h1 class="text-4xl text-center">Tic-Tac-Toe Game</h1>
    <div class="text-2xl pt-10">Match #{{ matchCounter }}</div>
    <div v-if="!gameIsOver" class="text-2xl py-10">
      Current player: {{ currentPlayer }}
    </div>
    <div v-else class="text-2xl py-10">Game over!</div>
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
      playerXWinsCount: 0
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

    checkVictory(playerNextMoves) {
      let victory = false;
      this.winningCombinations.forEach((winCombination) => {
        let goodMoves = 0;
        playerNextMoves.forEach((move) => {
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

    getPlayerPotentialBestMoves(playerNextMoves) {
      let playerPotentialBestMoves = [];

      this.winningCombinations.forEach((winCombination) => {
        let goodMoves = 0;

        playerNextMoves.forEach((move) => {
          if (winCombination.includes(move)) {
            goodMoves++;
          }
          if (goodMoves === 2) {
            const remainingMovesForWinCombination = winCombination.filter(
              (move) => !this.performedMoves.includes(move)
            );
            playerPotentialBestMoves.push(...remainingMovesForWinCombination);
          }
        });
      });

      return [...new Set(playerPotentialBestMoves)];
    },

    getBotNextMove() {
      const playerOplayerPotentialBestMoves = this.getPlayerPotentialBestMoves(
        this.playerOMoves
      );
      const playerXplayerPotentialBestMoves = this.getPlayerPotentialBestMoves(
        this.playerXMoves
      );

      let botPotentialNextMoves = [];

      if (playerXplayerPotentialBestMoves.length) {
        botPotentialNextMoves = playerXplayerPotentialBestMoves;
      } else if (playerOplayerPotentialBestMoves.length) {
        botPotentialNextMoves = playerOplayerPotentialBestMoves;
      } else {
        botPotentialNextMoves = [1, 2, 3, 4, 5, 6, 7, 8, 9].filter(
          (move) => !this.performedMoves.includes(move)
        );
      }

      return botPotentialNextMoves[
        Math.floor(Math.random() * botPotentialNextMoves.length)
      ];
    },

    botNextMove() {
      setTimeout(() => {
        const bootMove = this.getBotNextMove();
        this.playerXMoves.push(bootMove);
        const playerXWins = this.checkVictory(this.playerXMoves);
        if (playerXWins) {
          this.gameResult = `Player X WINS!`;
          this.playerXWinsCount++;
        } else {
          this.currentPlayer = "O";
        }
        this.performedMoves.push(bootMove);
      }, 150);
    },

    restartGame() {
      this.matchCounter++;
      this.currentPlayer = "O";
      this.playerOMoves = [];
      this.playerXMoves = [];
      this.winningCombination = [];
      this.performedMoves = [];
      this.gameResult = "It's a DRAW!";
      this.botNextMove();
    },

    playerNextMove(move) {
      this.playerOMoves.push(move);
      const playerOWins = this.checkVictory(this.playerOMoves);
      if (playerOWins) {
        this.gameResult = `Player O WINS!`;
        this.playerOWinsCount++;
      } else {
        this.currentPlayer = "X";
      }
      this.performedMoves.push(move);

      if (!playerOWins && this.performedMoves.length < 9) {
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
