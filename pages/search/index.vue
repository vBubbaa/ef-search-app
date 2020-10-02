<template>
  <div class="container px-4 sm:px-8 mx-auto">
    <!-- Header -->
    <div class="m-4 w-full text-center text-xl">
      Search For Food
    </div>
    <!-- Search and filter -->
    <div>
      <input
        class="w-full px-2 h-16 rounded focus:outline-none text-1xl border-green-400 border-2"
        type="search"
        placeholder="Search for food"
        v-on:keyup.enter="fetchFood"
        v-model="search"
      />
    </div>
    <!-- Results -->
    <div class="flex justify-between">
      <!-- Loading Spinner -->
      <div v-if="loading" class="text-center inline-flex py-4">
        <svg
          class="animate-spin -ml-1 mr-3 h-5 w-5 text-white"
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
        >
          <circle
            class="opacity-50"
            cx="12"
            cy="12"
            r="10"
            stroke="green"
            stroke-width="4"
          ></circle>
          <path
            class="opacity-75"
            fill="currentColor"
            d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"
          ></path>
        </svg>
        Loading Food
      </div>
      <!-- Done Loading -->
      <div v-else class="py-4">
        <div v-if="food.CountResults > 0">
          <div
            v-for="item in food.SearchResults"
            :key="item.DefaultVendorID"
            class="text-black"
          >
            {{ item.Desc1 }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
/**
 * The main search page for searching the provided API
 * Includes API search
 * Includes sorting and filtering on the search results
 */
export default {
  name: "search-index",
  data() {
    return {
      food: [],
      search: null,
      loading: false
    };
  },
  methods: {
    /**
     * Method to fetch food from given api based on user search query
     * Search 'q' is tied to data 'search'
     */
    fetchFood() {
      this.loading = true;
      this.food = this.$axios
        .get("", {
          params: { q: this.search }
        })
        .then(res => {
          console.log(res);
          this.food = res.data;
          this.loading = false;
        })
        .catch(err => {
          console.log(err);
        });
    }
  }
};
</script>

<style lang="scss" scoped></style>
