<template>
  <div>
    {{ title }}
    <select
      class="block appearance-none w-full bg-white border border-green-400 hover:border-green-500 px-4 py-2 pr-8 my-4 rounded shadow leading-tight focus:outline-none focus:shadow-outline"
      @change="emitSelect($event)"
    >
      <!-- Will return an original list of the food (no filters) -->
      <option value="None">None</option>
      <!-- Iterate through calculated UNIQUE brands from a list of food -->
      <option v-for="(opt, key) in options" :key="key" :value="opt">
        {{ opt }}
      </option>
    </select>
  </div>
</template>

<script>
/**
 * Selection component
 * Used for selecting filters
 */
export default {
  name: "inputselection",
  props: {
    title: String,
    options: Array,
  },
  methods: {
    // Emit the event on selection
    emitSelect(selection) {
      this.$emit("selected", selection.target.value);
    },
  },
};
</script>

<style lang="scss" scoped>
</style>