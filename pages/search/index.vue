<template>
  <div class="container px-4 sm:px-8 mx-auto">
    <!-- Header -->
    <div class="m-4 w-full text-center text-xl">Search For Food</div>
    <!-- Search and filter -->
    <div>
      <input
        class="w-full px-6 h-16 rounded focus:outline-none text-1xl border-green-400 border-2 rounded-full"
        type="search"
        placeholder="Search for food"
        v-on:keyup.enter="fetchFood"
        v-model="search"
      />
    </div>
    <!-- Results -->
    <div class="flex justify-between">
      <!-- Loading Spinner -->
      <div v-if="loading" class="text-center inline-flex py-2">
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
      <div v-else class="py-2">
        <!-- If we have food results from the api give the option to filter/sort -->
        <div v-if="food != null">
          <div class="inline-block relative w-64">
            <!-- Selection list for selecting brands to filter by -->
            <select
              class="block appearance-none w-full bg-white border border-green-400 hover:border-green-500 px-4 py-2 pr-8 my-4 rounded shadow leading-tight focus:outline-none focus:shadow-outline"
              v-model="filterOptions.brandSelection"
              @change="filterByBrand($event)"
            >
              Filter by Brand
              <!-- Options placeholder that is hidden -->
              <option :value="null" disabled hidden>Brands</option>
              <!-- Will return an original list of the food (no filters) -->
              <option value="None">None</option>
              <!-- Iterate through calculated UNIQUE brands from a list of food -->
              <option
                v-for="(brand, key) in filterOptions.brandsAvailable"
                :key="key"
                :value="brand"
              >
                {{ brand }}
              </option>
            </select>
            <div
              class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700"
            >
              <svg
                class="fill-current h-4 w-4"
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 20 20"
              >
                <path
                  d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
                />
              </svg>
            </div>
          </div>
          <!-- If filtering has happened, our filteredFood list will not be empty, so we show the filtered food items -->
          <div v-if="this.filteredFood != null">
            <div
              v-for="item in filteredFood"
              :key="item.DefaultVendorID"
              class="text-black border-2 border-green-400"
            >
              {{ item.Desc1 }}
              <br />
              {{ item.Brand.Desc1 }}
            </div>
          </div>
          <!-- Filtering has NOT happened, so we show the original list of food items from the API response -->
          <div
            v-else
            v-for="item in food.SearchResults"
            :key="item.DefaultVendorID"
            class="text-black border-2 border-green-400"
          >
            {{ item.Desc1 }}
            <br />
            {{ item.Brand.Desc1 }}
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
 * Includes Brand filters given a food search response
 */
export default {
  name: "search-index",
  data() {
    return {
      // Set of food reuturned from API response
      food: null,
      // A filtered list of food
      filteredFood: null,
      // Search query
      search: null,
      // Track loading from API
      loading: false,
      // Variables we will use for filtering
      filterOptions: {
        // Tracks a brand that is selected from our brand <select>
        brandSelection: null,
        // This will be filled with a UNIQUE list of brands (We have to remove deplicates)
        brandsAvailable: ["None"],
      },
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
          params: { q: this.search },
        })
        .then((res) => {
          console.log(res);
          this.food = res.data;
          this.buildBrands();
          this.loading = false;
        })
        .catch((err) => {
          console.log(err);
        });
    },

    /**
     * buildBrands() iterates the food list, and builds a list of brands we will filter by
     * We have to seperately build this list so that we can remove duplicate values
     * Many search results have the same brands*
     * We store each brand to a list, then create a new set which creates a unique list of brands
     */
    buildBrands() {
      let tempBrands = [];
      this.food.SearchResults.forEach(function (foodItem) {
        console.log(foodItem.Brand.Desc1);
        tempBrands.push(foodItem.Brand.Desc1);
      });
      this.filterOptions.brandsAvailable = new Set(tempBrands);
    },

    /**
     * Recieves our option selection for our brand list dropdown
     * Returns a copy of food 'filteredFood' that consists of whichever brand is selected
     * If the option 'none' is selected, we set the filteredFood list to the original food list (no filtering)
     */
    filterByBrand(brandselection) {
      if (brandselection.target.value !== "None") {
        this.filteredFood = this.food.SearchResults.filter(
          (foodItem) => foodItem.Brand.Desc1 === brandselection.target.value
        );
      } else {
        this.filteredFood = this.food.SearchResults;
      }
    },
  },
};
</script>

<style lang="scss" scoped></style>
