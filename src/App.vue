<template>
  <div class="min-h-screen flex items-center justify-center">
    <div class="text-center">
      <h1 class="text-3xl px-2">Tic-Tac-Toe Game</h1>
      <div class="text-2xl pt-5 md:pt-10">Match #{{ matchCounter }}</div>
      <div class="flex justify-between col-span-12">
        <GameMode @change-game-mode="onChangeGameMode" />
        <WhoStartsNextGame :game-mode="selectedGameMode" @change-selected-player="onChangeSelectedPlayer" />
      </div>
      <GameBoard :selectedPlayer="selectedPlayer" :selectedGameMode="selectedGameMode" :startNewGame="startNewGame" @player-wins="onPlayerWins"
        @game-over="onGameOver" />
      <GameScore :playerOWins="playerOWinsCount" :playerXWins="playerXWinsCount" />
      <GameOverDialog :gameIsOver="gameIsOver" :gameResult="gameResult" @restart-game="restartGame" />
    </div>
  </div>
</template>

<script>
import GameMode from "./components/GameMode.vue";
import WhoStartsNextGame from "./components/WhoStartsNextGame.vue";
import GameBoard from "./components/GameBoard.vue";
import GameScore from "./components/GameScore.vue";
import GameOverDialog from "./components/GameOverDialog.vue";

export default {
  components: {
    GameMode,
    WhoStartsNextGame,
    GameBoard,
    GameScore,
    GameOverDialog
  },

  data() {
    return {
      gameResult: "It's a DRAW!",
      gameIsOver: false,
      matchCounter: 1,
      playerOWinsCount: 0,
      playerXWinsCount: 0,
      selectedPlayer: "X",
      selectedGameMode: "single",
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

    onChangeGameMode(currentGameMode) {
      this.selectedGameMode = currentGameMode;
      this.matchCounter = 1;
      this.playerOWinsCount = 0;
      this.playerXWinsCount = 0;
    },

    restartGame() {
      this.matchCounter++;
      this.gameResult = "It's a DRAW!";
      this.startNewGame = !this.startNewGame;
      this.gameIsOver = false;
    }
  }
};
</script>
