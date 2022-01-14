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
            @field-clicked="playerNextMove(n)"
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
      performedMoves: [],
      gameResult: "It's a DRAW!"
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
      if (this.player1Moves.includes(field)) {
        return "O";
      }
      if (this.player2Moves.includes(field)) {
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
      const player1playerPotentialBestMoves = this.getPlayerPotentialBestMoves(
        this.player1Moves
      );
      const player2playerPotentialBestMoves = this.getPlayerPotentialBestMoves(
        this.player2Moves
      );

      let botPotentialNextMoves = [];

      if (player2playerPotentialBestMoves.length) {
        botPotentialNextMoves = player2playerPotentialBestMoves;
      } else if (player1playerPotentialBestMoves.length) {
        botPotentialNextMoves = player1playerPotentialBestMoves;
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
        this.player2Moves.push(bootMove);
        const player2Wins = this.checkVictory(this.player2Moves);
        if (player2Wins) {
          this.gameResult = `Player X WINS!`;
        } else {
          this.currentPlayer = "O";
        }
        this.performedMoves.push(bootMove);
      }, 150);
    },

    restartGame() {
      this.currentPlayer = "O";
      this.player1Moves = [];
      this.player2Moves = [];
      this.winningCombination = [];
      this.performedMoves = [];
      this.gameResult = "It's a DRAW!";
      this.botNextMove();
    },

    playerNextMove(move) {
      this.player1Moves.push(move);
      const player1Wins = this.checkVictory(this.player1Moves);
      if (player1Wins) {
        this.gameResult = `Player O WINS!`;
      } else {
        this.currentPlayer = "X";
      }
      this.performedMoves.push(move);

      if (!player1Wins && this.performedMoves.length < 9) {
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
