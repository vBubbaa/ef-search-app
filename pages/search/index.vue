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
      <div v-else class="py-2 w-full">
        <!-- If we have food results from the api give the option to filter/sort -->
        <div v-if="food != null" class="w-full">
          <div class="inline-flex flex flex-col w-full">
            <!-- Input selection for selecting type of filter -->
            <InputSelection
              :title="'Select a filter'"
              :options="filterOptions.filters"
              @selected="selectFilter"
            />
            <!-- Input selection for selecting the built filter options -->
            <InputSelection
              v-if="filterOptions.selectedFilter != null"
              :title="filterOptions.selectFilter"
              :options="filterOptions.builtFilters"
              @selected="filterFood"
            />
          </div>
          <!-- Sorting section -->
          <div class="inline-flex flex flex-row justify-around w-full pb-4">
            <Sort
              @sort="handleSort"
              :title="s"
              v-for="(s, k) in sortOptions.sorts"
              :key="k"
            />
          </div>
          <div v-if="this.filteredFood != null" class="mb-4 w-full">
            <div class="flex flex-wrap justify-between overflow-hidden">
              <!-- Iterate filtered food and show food item card for each filtered food -->
              <FoodItemCard
                v-for="(item, key) in filteredFood"
                :key="key"
                :fooditem="item"
              />
            </div>
          </div>
          <!-- Filtering has NOT happened, so we show the original list of food items from the API response -->
          <div v-else class="mb-4 w-full">
            <div class="flex flex-wrap justify-between overflow-hidden">
              <!-- Iterate base food (inital unfiltered API res data) -->
              <FoodItemCard
                v-for="(item, key) in food.SearchResults"
                :key="key"
                :fooditem="item"
              />
            </div>
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
import InputSelection from "../../components/functional/InputSelection";
import Sort from "../../components/functional/Sort";
import FoodItemCard from "../../components/ui/FoodItemCard";

export default {
  name: "search-index",
  components: {
    InputSelection,
    Sort,
    FoodItemCard,
  },
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
        // Available Filters
        filters: ["Brands", "Package Size"],
        // The selected filter in string format
        selectedFilter: null,
        // Generated filter options based on filter selected
        builtFilters: ["None"],
      },
      // Options for sorting
      sortOptions: {
        // Available sorting methods
        sorts: ["ABBScore", "Alphabetical"],
        // Default sort by descending
        descending: true,
      },
    };
  },
  methods: {
    /**
     * Method to fetch food from given api based on user search query
     * Search 'q' is tied to data 'search'
     */
    fetchFood() {
      this.clearFilters();
      this.loading = true;
      this.food = this.$axios
        .get("", {
          params: { q: this.search },
        })
        .then((res) => {
          this.food = res.data;
          this.loading = false;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    /**
     * Catches a sort emitted event, and sorts accordingly
     * Available sorts are "ABBScore" and "Alphabetical"
     */
    handleSort(sortType) {
      if (sortType == "ABBScore") {
        // Filter by ABBScore desc/asc
        this.sortOptions.descending = !this.sortOptions.descending;
        this.filteredFood = this.food.SearchResults.sort((a, b) => {
          if (this.sortOptions.descending) {
            return a.ABBScore > b.ABBScore ? -1 : 1;
          } else {
            return a.ABBScore < b.ABBScore ? -1 : 1;
          }
        });
      } else if (sortType == "Alphabetical") {
        this.sortOptions.descending = !this.sortOptions.descending;
        this.filteredFood = this.food.SearchResults.sort((a, b) => {
          if (this.sortOptions.descending) {
            return a.Desc1 > b.Desc1 ? -1 : 1;
          } else {
            return a.Desc1 < b.Desc1 ? -1 : 1;
          }
        });
      }
    },
    // Grabs the selected filter to filter by from <InputSelection />
    selectFilter(selection) {
      this.clearFilters();
      this.filterOptions.selectedFilter = selection;
      this.buildFilterOptions(selection);
    },
    /**
     * Grabs the selected filter and generates the selection options to choose from
     * We store all options to an array, then cast it to a set removing non-unique values
     * We then convert it back to an array
     */
    buildFilterOptions(filter) {
      if (filter == "Brands") {
        let tempBrands = [];
        this.food.SearchResults.forEach(function (foodItem) {
          tempBrands.push(foodItem.Brand.Desc1);
        });
        let uniqueSet = new Set(tempBrands);
        this.filterOptions.builtFilters = [...uniqueSet];
      } else if (filter == "Package Size") {
        let tempSizes = [];
        this.food.SearchResults.forEach(function (foodItem) {
          if (foodItem.PackagingSize != null) {
            tempSizes.push(foodItem.PackagingSize);
          }
        });
        let uniqueSet = new Set(tempSizes);
        this.filterOptions.builtFilters = [...uniqueSet];
      }
    },
    /**
     * Grabs whichever filter options is selected
     * Checks to see which filter is selected
     * Sets the food to a filtered list of food items
     */
    filterFood(selection) {
      if (this.filterOptions.selectedFilter == "Brands") {
        if (selection != "None") {
          this.filteredFood = this.food.SearchResults.filter(
            (foodItem) => foodItem.Brand.Desc1 === selection
          );
        } else {
          this.filteredFood = this.food.SearchResults;
        }
      } else if (this.filterOptions.selectedFilter == "Package Size") {
        if (selection != "None") {
          this.filteredFood = this.food.SearchResults.filter(
            (foodItem) => foodItem.PackagingSize === selection
          );
        } else {
          this.filteredFood = this.food.SearchResults;
        }
      }
    },
    /**
     * Clear all filters
     * Used when manually clearing, or when selecting a different filter
     */
    clearFilters() {
      this.filterOptions.selectedFilter = null;
      this.filterOptions.builtFilters = ["None"];
      this.filteredFood = null;
    },
  },
};
</script>

<style lang="scss" scoped></style>
