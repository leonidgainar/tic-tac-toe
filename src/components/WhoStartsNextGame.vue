<template>
  <div class="flex justify-start col-span-6">
    <div>
      <h2 class="text-xl pt-5">Who starts the next game?</h2>
      <div class="text-left">
        <div class="form-check pt-5">
          <input
            class="form-check-input appearance-none rounded-full h-4 w-4 border border-gray-300 bg-white checked:bg-blue-600 checked:border-blue-600 focus:outline-none align-top mt-1 mr-2 cursor-pointer"
            type="radio" v-model="currentSelectedPlayer" value="O" id="playerO" />
          <label class="form-check-label inline-block text-gray-800" for="playerO">
            {{ player1Label }}
          </label>
        </div>
        <div class="form-check pt-3 pb-5">
          <input
            class="form-check-input appearance-none rounded-full h-4 w-4 border border-gray-300 bg-white checked:bg-red-600 checked:border-red-600 focus:outline-none align-top mt-1 mr-2 cursor-pointer"
            type="radio" v-model="currentSelectedPlayer" value="X" id="playerX" />
          <label class="form-check-label inline-block text-gray-800" for="playerX">
            {{ player2Label }}
          </label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    gameMode: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      currentSelectedPlayer: "X",
    };
  },

  watch: {
    currentSelectedPlayer() {
      this.$emit("change-selected-player", this.currentSelectedPlayer);
    }
  },

  computed: {
    player1Label() {
      return this.gameMode === "single" ? "You (Player O)" : "Player O";
    },

    player2Label() {
      return this.gameMode === "single" ? "Bot (Player X)" : "Player X";
    }
  }
};
</script>
