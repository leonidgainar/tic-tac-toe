<template>
  <div class="min-h-screen flex items-center justify-center">
    <div class="text-center">
      <h1 class="text-3xl px-2">Tic-Tac-Toe Game</h1>
      <div class="text-2xl pt-5 md:pt-10">Match #{{ matchCounter }}</div>
      <div class="flex justify-center">
        <div>
          <h2 class="text-xl pt-5">Who starts the next game?</h2>
          <SelectPlayerRadioButtons
            @change-selected-player="onChangeSelectedPlayer"
          />
        </div>
      </div>
      <GameBoard
        :selectedPlayer="selectedPlayer"
        :restartGame="startNewGame"
        @player-wins="onPlayerWins"
        @game-over="onGameOver"
      />
      <GameScore
        :playerOWins="playerOWinsCount"
        :playerXWins="playerXWinsCount"
      />
      <GameOverDialog
        :gameIsOver="gameIsOver"
        :gameResult="gameResult"
        @restart-game="restartGame"
      />
    </div>
  </div>
</template>

<script>
import GameOverDialog from "./components/GameOverDialog.vue";
import SelectPlayerRadioButtons from "./components/SelectPlayerRadioButtons.vue";
import GameScore from "./components/GameScore.vue";
import GameBoard from "./components/GameBoard.vue";

export default {
  components: {
    GameOverDialog,
    SelectPlayerRadioButtons,
    GameScore,
    GameBoard
  },

  data() {
    return {
      gameResult: "It's a DRAW!",
      gameIsOver: false,
      matchCounter: 1,
      playerOWinsCount: 0,
      playerXWinsCount: 0,
      selectedPlayer: "X",
      startNewGame: false
    };
  },

  methods: {
    onPlayerWins(winner) {
      this.gameResult = `Player ${winner} WINS!`;
      if (winner === "X") {
        this.playerXWinsCount++;
      }
      if (winner === "O") {
        this.playerOWinsCount++;
      }
      this.gameIsOver = true;
    },

    onGameOver() {
      this.gameIsOver = true;
    },

    onChangeSelectedPlayer(currentPlayer) {
      this.selectedPlayer = currentPlayer;
    },

    restartGame() {
      this.matchCounter++;
      this.currentPlayer = "O";
      this.gameResult = "It's a DRAW!";
      this.startNewGame = !this.startNewGame;
      this.gameIsOver = false;
    }
  }
};
</script>
