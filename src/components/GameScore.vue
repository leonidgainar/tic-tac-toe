<template>
  <div class="text-2xl py-10">
    <span class="text-blue-700">Player O</span>
    <span class="text-4xl px-4"> {{ playerOWins }} : {{ playerXWins }} </span>
    <span class="text-red-700">Player X</span>
  </div>
</template>

<script>
export default {
  props: {
    playerOWins: {
      type: Number,
      default: 0
    },
    playerXWins: {
      type: Number,
      default: 0
    }
  }
};
</script>
