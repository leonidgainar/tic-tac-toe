<template>
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
      currentPlayer: "O",
      humanPlayerMoves: [],
      botPlayerMoves: [],
      winningCombination: [],
      performedMoves: []
    };
  },

  watch: {
    startNewGame() {
      this.currentPlayer = "O";
      this.humanPlayerMoves = [];
      this.botPlayerMoves = [];
      this.winningCombination = [];
      this.performedMoves = [];
      if (this.selectedPlayer === "X") {
        this.botNextMove();
      }
    }
  },

  mounted() {
    this.botNextMove();
  },

  methods: {
    setPlayerSymbol(field) {
      if (this.humanPlayerMoves.includes(field)) {
        return "O";
      }
      if (this.botPlayerMoves.includes(field)) {
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
      const playerNextBestMoves = this.getNextBestMoves(this.humanPlayerMoves);
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

    playerNextMove(move) {
      this.humanPlayerMoves.push(move);
      const humanPlayer = this.checkVictory(this.humanPlayerMoves);
      if (humanPlayer) {
        this.$emit("player-wins", this.currentPlayer);
      } else {
        this.currentPlayer = "X";
      }
      this.performedMoves.push(move);
      if (this.performedMoves.length === 9) {
        this.$emit("game-over");
      }
      if (!humanPlayer) {
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
