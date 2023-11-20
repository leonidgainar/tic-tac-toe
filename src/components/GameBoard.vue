<template>
  <div class="min-w-full">
    <div class="bg-blue-200 shadow-xl rounded-lg">
      <div class="grid grid-cols-3 gap-1 md:gap-2 text-4xl md:text-6xl p-5">
        <GameBoardField
          v-for="n in 9"
          :key="n"
          :playerSymbol="setPlayerSymbol(n)"
          :textColor="setTextColor(n)"
          @field-clicked="currentPlayer === 'O' ? player1NextMove(n) : player2NextMove(n)"
        />
      </div>
    </div>
  </div>
</template>

<script>
import GameBoardField from "./GameBoardField.vue";

export default {
  components: {
    GameBoardField
  },

  props: {
    selectedPlayer: {
      type: String,
      default: "X"
    },
    selectedGameMode: {
      type: String,
      default: "single"
    },
    startNewGame: {
      type: Boolean,
      default: false
    }
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
      currentPlayer: this.selectedPlayer,
      player1Moves: [],
      player2Moves: [],
      botPlayerMoves: [],
      winningCombination: [],
      performedMoves: []
    };
  },

  watch: {
    startNewGame() {
      this.currentPlayer = this.selectedPlayer;
      this.botPlayerMoves = [];
      this.player1Moves = [];
      this.player2Moves = [];
      this.winningCombination = [];
      this.performedMoves = [];
      if (this.selectedPlayer === "X" && this.selectedGameMode === "single") {
        this.botNextMove();
      }
    }
  },

  mounted() {
    if (this.selectedGameMode === "single") {
      this.botNextMove();
    }
  },

  methods: {
    setPlayerSymbol(field) {
      if (this.player1Moves.includes(field)) {
        return "O";
      }
      if (this.botPlayerMoves.includes(field) || this.player2Moves.includes(field)) {
        return "X";
      }
    },

    checkVictory(playerMoves) {
      let victory = false;
      this.winningCombinations.forEach((winCombination) => {
        let goodMovesCount = 0;
        playerMoves.forEach((move) => {
          if (winCombination.includes(move)) {
            goodMovesCount++;
          }
          if (goodMovesCount === 3) {
            victory = true;
            this.winningCombination = winCombination;
            this.$emit("game-over");
          }
        });
      });
      return victory;
    },

    getNextBestMoves(playerMoves) {
      let nextBestMoves = [];

      this.winningCombinations.forEach((winCombination) => {
        let goodMovesCount = 0;

        playerMoves.forEach((move) => {
          if (winCombination.includes(move)) {
            goodMovesCount++;
          }
          if (goodMovesCount === 2) {
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
      let botNextMoves = [];
      const playerNextBestMoves = this.getNextBestMoves(this.player1Moves);
      const botNextBestMoves = this.getNextBestMoves(this.botPlayerMoves);

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
        this.botPlayerMoves.push(bootMove);
        const botPlayerWins = this.checkVictory(this.botPlayerMoves);
        if (botPlayerWins) {
          this.$emit("player-wins", this.currentPlayer);
        } else {
          this.currentPlayer = "O";
        }
        this.performedMoves.push(bootMove);
        if (this.performedMoves.length === 9) {
          this.$emit("game-over");
        }
      }, 150);
    },

    player1NextMove(move) {
      this.player1Moves.push(move);
      const player1Wins = this.checkVictory(this.player1Moves);
      if (player1Wins) {
        this.$emit("player-wins", this.currentPlayer);
      } else {
        this.currentPlayer = "X";
      }
      this.performedMoves.push(move);
      if (this.performedMoves.length === 9) {
        this.$emit("game-over");
      }
      if (!player1Wins && this.selectedGameMode === "single") {
        this.botNextMove();
      }
    },

    player2NextMove(move) {
      this.player2Moves.push(move);
      const player2Wins = this.checkVictory(this.player2Moves);
      if (player2Wins) {
        this.$emit("player-wins", this.currentPlayer);
      } else {
        this.currentPlayer = "O";
      }
      this.performedMoves.push(move);
      if (this.performedMoves.length === 9) {
        this.$emit("game-over");
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
