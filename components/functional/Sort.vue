<template>
  <div class="w-full flex-row align-center flex justify-center">
    <div class="px-2">
      {{ title }}
    </div>
    <button
      class="border-b-2 border-green-400 focus:outline-none hover:font-semibold"
      @click="emitSort"
    >
      Sort
    </button>
  </div>
</template>

<script>
/**
 * Sorting component
 * Dispatches sort event that the search page catches
 * Takes the event val, and sorts accordingly
 */
export default {
  name: "sort",
  props: {
    title: String,
  },
  methods: {
    // Emit the event on selection
    emitSort() {
      this.$emit("sort", this.title);
    },
  },
};
</script>

<style lang="scss" scoped>
</style>