<template>
  <button
    class="h-20 md:h-44 w-20 md:w-44 bg-blue-500 hover:bg-blue-400 rounded-md py-5 md:py-10 px-6 md:px-16"
    :class="textColor"
    aria-label="field"
    :disabled="fieldIsChecked"
    @click="$emit('field-clicked')"
  >
    {{ playerSymbol }}
  </button>
</template>

<script>
export default {
  props: {
    playerSymbol: {
      type: String
    },
    textColor: {
      type: String,
      default: "text-white"
    }
  },

  computed: {
    fieldIsChecked() {
      return this.playerSymbol === "X" || this.playerSymbol === "O";
    }
  }
};
</script>
