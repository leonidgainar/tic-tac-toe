<template>
  <div class="text-center">
    <h1 class="text-4xl text-center">Tic-Tac-Toe Game</h1>
    <h3 v-if="!gameIsOver" class="text-3xl py-10">
      Current player: {{ currentPlayer }}
    </h3>
    <h3 v-else class="text-3xl py-10">Game is over!</h3>
    <div class="min-w-full">
      <div class="bg-blue-200 shadow-xl rounded-lg">
        <div class="grid grid-cols-3 gap-1 md:gap-2 text-4xl md:text-6xl p-5">
          <GameBoardField
            v-for="n in 9"
            :key="n"
            :playerSymbol="setPlayerSymbol(n)"
            :textColor="setTextColor(n)"
            @field-clicked="playerClickedField(n)"
          />
        </div>
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
      player1Moves: [],
      player2Moves: [],
      winningCombination: [],
      usedFields: [],
      gameResult: "It's a DRAW!"
    };
  },

  computed: {
    gameIsOver() {
      return this.winningCombination.length > 0 || this.usedFields.length === 9;
    }
  },

  methods: {
    setPlayerSymbol(field) {
      if (this.player1Moves.includes(field)) {
        return "O";
      }
      if (this.player2Moves.includes(field)) {
        return "X";
      }
    },

    checkVictory(playerClickedFields) {
      let victory = false;
      this.winningCombinations.forEach((winCombination) => {
        let movesToWin = 0;
        playerClickedFields.forEach((move) => {
          if (winCombination.includes(move)) {
            movesToWin++;
          }
          if (movesToWin === 3) {
            victory = true;
            this.winningCombination = winCombination;
          }
        });
      });
      return victory;
    },

    getPlayerBestCombination(playerMoves) {
      let maxMovesForWin = 0;
      let bestCombination = [];

      this.winningCombinations.forEach((winCombination) => {
        let movesForWin = 0;

        playerMoves.forEach((move) => {
          if (winCombination.includes(move)) {
            movesForWin++;
          }
          if (maxMovesForWin <= movesForWin) {
            maxMovesForWin = movesForWin;
            bestCombination = winCombination;
          }
        });
      });

      return { maxMovesForWin, bestCombination };
    },

    getBotNextMove() {
      const player1Stats = this.getPlayerBestCombination(this.player1Moves);
      const player2Stats = this.getPlayerBestCombination(this.player2Moves);

      let potentialNextMoves = [];

      if (player1Stats.maxMovesForWin > player2Stats.maxMovesForWin) {
        potentialNextMoves = player1Stats.bestCombination.filter(
          (move) => !this.usedFields.includes(move)
        );
      } else {
        potentialNextMoves = player2Stats.bestCombination.filter(
          (move) => !this.usedFields.includes(move)
        );
      }

      if (!potentialNextMoves.length) {
        potentialNextMoves = [1, 2, 3, 4, 5, 6, 7, 8, 9].filter(
          (move) => !this.usedFields.includes(move)
        );
      }

      return potentialNextMoves[
        Math.floor(Math.random() * potentialNextMoves.length)
      ];
    },

    botNextMove() {
      setTimeout(() => {
        const bootMove = this.getBotNextMove();
        this.player2Moves.push(bootMove);
        const player2Wins = this.checkVictory(this.player2Moves);
        if (player2Wins) {
          this.gameResult = `Player X WINS!`;
        } else {
          this.currentPlayer = "O";
        }
        this.usedFields.push(bootMove);
      }, 500);
    },

    restartGame() {
      this.currentPlayer = "O";
      this.player1Moves = [];
      this.player2Moves = [];
      this.winningCombination = [];
      this.usedFields = [];
      this.gameResult = "It's a DRAW!";
    },

    playerClickedField(field) {
      this.player1Moves.push(field);
      const player1Wins = this.checkVictory(this.player1Moves);
      if (player1Wins) {
        this.gameResult = `Player O WINS!`;
      } else {
        this.currentPlayer = "X";
      }
      this.usedFields.push(field);

      if (!player1Wins && this.usedFields.length < 9) {
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
