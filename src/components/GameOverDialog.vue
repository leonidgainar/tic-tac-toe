<template>
  <div
    class="fixed z-10 inset-0 overflow-y-auto mt-10"
    aria-labelledby="modal-title"
    role="dialog"
    aria-modal="true"
    v-if="gameIsOver"
  >
    <div class="flex items-top justify-center px-4">
      <div
        class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity"
        aria-hidden="true"
      ></div>

      <span class="sm:inline-block" aria-hidden="true"> &#8203; </span>

      <div
        class="inline-block bg-white rounded-lg text-center overflow-hidden shadow-xl transform transition-all sm:max-w-lg w-full"
      >
        <div class="bg-white px-4 py-5">
          <h3 class="text-3xl font-medium text-gray-900">
            {{ gameResult }}
          </h3>
          <div class="mt-10">
            <button
              class="bg-blue-500 hover:bg-blue-700 text-white text-xl font-bold py-4 px-4 rounded mb-5"
              @click="$emit('restart-game')"
            >
              Restart Game
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    gameIsOver: {
      type: Boolean,
      default: false
    },
    gameResult: {
      type: String,
      default: "Game is Over"
    }
  }
};
</script>
