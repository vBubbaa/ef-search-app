<template>
  <div class="w-full sm:w-5/12 md:5/12 p-2 mb-2 border-2 border-green-400 h-48">
    <!-- Description of food item -->
    <div class="w-full font-semibold mb-2 truncate">
      {{ fooditem.Desc1 }}
    </div>
    <hr />
    <div class="pt-2">
      Brand:
      <!-- If the brand has a link, include it | else just show the brand name -->
      <div v-if="fooditem.Brand.WebAddress" class="inline-block">
        <a
          :href="fooditem.Brand.WebAddress"
          class="text-green-400 hover:text-green-500"
          >{{ fooditem.Brand.Desc1 }}</a
        >
      </div>
      <div v-else class="inline-block">N/A</div>
    </div>
    <!-- ABBScore -->
    <div class="pt-2">ABBScore: {{ fooditem.ABBScore }}</div>
    <!-- Package Size -->
    <div class="pt-2">
      Package Size:
      <span v-if="fooditem.PackagingSize">{{ fooditem.PackagingSize }}</span
      ><span v-else>N/A</span>
    </div>
  </div>
</template>

<script>
/**
 * Food Item Card showing:
 * Desc1, Brand.Desc1, ABBScore, PackagingSize
 * Takes in fooditem object as prop
 */
export default {
  name: "fooditemcard",
  props: {
    fooditem: Object,
  },
};
</script>

<style lang="scss" scoped>
</style>