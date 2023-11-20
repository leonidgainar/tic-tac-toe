<template>
  <div class="flex justify-start basis-full md:basis-1/2">
    <div>
      <h2 class="text-xl pt-5">Select game mode:</h2>
      <div class="text-left">
        <div class="form-check pt-5">
          <input
            class="form-check-input appearance-none rounded-full h-4 w-4 border border-gray-300 bg-white checked:bg-blue-600 checked:border-blue-600 focus:outline-none align-top mt-1 mr-2 cursor-pointer"
            type="radio" v-model="currentGameMode" value="single" id="single" />
          <label class="form-check-label inline-block text-gray-800" for="single">
            With Bot
          </label>
        </div>
        <div class="form-check pt-3 pb-5">
          <input
            class="form-check-input appearance-none rounded-full h-4 w-4 border border-gray-300 bg-white checked:bg-red-600 checked:border-red-600 focus:outline-none align-top mt-1 mr-2 cursor-pointer"
            type="radio" v-model="currentGameMode" value="multiplayer" id="multiplayer" />
          <label class="form-check-label inline-block text-gray-800" for="multiplayer">
            Two players
          </label>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
export default {
  data() {
    return {
      currentGameMode: "single"
    };
  },

  watch: {
    currentGameMode() {
      this.$emit("change-game-mode", this.currentGameMode);
    }
  }
};
</script>
