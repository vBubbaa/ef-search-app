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
    <!-- Loading Spinner -->
    <div v-if="loading">
      <Loading />
    </div>
    <!-- Results -->
    <div v-else class="flex justify-between">
      <!-- Done Loading -->
      <div class="py-2 w-full">
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
import Loading from "../../components/ui/Loading";

export default {
  name: "search-index",
  components: {
    InputSelection,
    Sort,
    FoodItemCard,
    Loading
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
        builtFilters: ["None"]
      },
      // Options for sorting
      sortOptions: {
        // Available sorting methods
        sorts: ["ABBScore", "Alphabetical"],
        // Default sort by descending
        descending: true
      }
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
          params: { q: this.search }
        })
        .then(res => {
          this.food = res.data;
          this.loading = false;
        })
        .catch(err => {
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
        // Check if we are sorting the base food items, or filtered food items
        if (this.filteredFood == null) {
          this.filteredFood = this.food.SearchResults.sort((a, b) => {
            if (this.sortOptions.descending) {
              return a.ABBScore > b.ABBScore ? -1 : 1;
            } else {
              return a.ABBScore < b.ABBScore ? -1 : 1;
            }
          });
        }
        // Sort by filtered food (not base food response)
        else {
          this.filteredFood = this.filteredFood.sort((a, b) => {
            if (this.sortOptions.descending) {
              return a.ABBScore > b.ABBScore ? -1 : 1;
            } else {
              return a.ABBScore < b.ABBScore ? -1 : 1;
            }
          });
        }
      } else if (sortType == "Alphabetical") {
        this.sortOptions.descending = !this.sortOptions.descending;
        // Check if we are sorting the base food items, or filtered food items
        if (this.filteredFood == null) {
          this.filteredFood = this.food.SearchResults.sort((a, b) => {
            if (this.sortOptions.descending) {
              return a.Desc1 > b.Desc1 ? -1 : 1;
            } else {
              return a.Desc1 < b.Desc1 ? -1 : 1;
            }
          });
        }
        // Sort filteredfood (not base food response)
        else {
          this.filteredFood = this.filteredFood.sort((a, b) => {
            if (this.sortOptions.descending) {
              return a.Desc1 > b.Desc1 ? -1 : 1;
            } else {
              return a.Desc1 < b.Desc1 ? -1 : 1;
            }
          });
        }
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
        // Temp placeholders we will use to extract unique values
        let tempBrands = [];
        // Iterate food items and push each to the temp array
        this.food.SearchResults.forEach(function(foodItem) {
          tempBrands.push(foodItem.Brand.Desc1);
        });
        // Convert to set() and remove non-unique values
        let uniqueSet = new Set(tempBrands);
        // Spread the set and convert it back to an array
        this.filterOptions.builtFilters = [...uniqueSet];
      } else if (filter == "Package Size") {
        // Temp placeholders we will use to extract unique values
        let tempSizes = [];
        // Iterate food items and push each to the temp array
        this.food.SearchResults.forEach(function(foodItem) {
          if (foodItem.PackagingSize != null) {
            tempSizes.push(foodItem.PackagingSize);
          }
        });
        // Convert to set() and remove non-unique values
        let uniqueSet = new Set(tempSizes);
        // Spread the set and convert it back to an array
        this.filterOptions.builtFilters = [...uniqueSet];
      }
    },
    /**
     * Grabs whichever filter options is selected
     * Checks to see which filter is selected
     * Sets the food to a filtered list of food items
     */
    filterFood(selection) {
      // Filter by brands
      if (this.filterOptions.selectedFilter == "Brands") {
        // Check if selection is not none first
        if (selection != "None") {
          // Filter by selection value
          this.filteredFood = this.food.SearchResults.filter(
            foodItem => foodItem.Brand.Desc1 === selection
          );
        } else {
          this.filteredFood = this.food.SearchResults;
        }
      }
      // Filter by Package Size
      else if (this.filterOptions.selectedFilter == "Package Size") {
        // Check if selection is not none first
        if (selection != "None") {
          // Filter by selection value
          this.filteredFood = this.food.SearchResults.filter(
            foodItem => foodItem.PackagingSize === selection
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
    }
  }
};
</script>

<style lang="scss" scoped></style>
