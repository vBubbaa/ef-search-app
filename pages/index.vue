<template>
  <div class="h-full w-full">
    <!-- Header Image with search link + brief about us -->
    <div class="h-64 bg-bagel-background bg-cover">
      <div
        class="container h-full min-w-full p-4 sm:p-8 text-center flex flex-col justify-center align-middle"
      >
        <!-- Headline -->
        <div class="text-center text-white font-extrabold text-3xl w-full p-2">
          Search Everything Food's extensive collection of foods.
        </div>
        <!-- Call to action (search page) -->
        <div class="text-white font-semibold text-xl p-2">
          <button
            class="bg-green-400 text-black rounded-full py-2 px-4 hover:bg-green-500 focus:outline-none"
          >
            Search
          </button>
        </div>
      </div>
    </div>
    <div class="w-full">
      <AboutProject />
    </div>
  </div>
</template>

<script>
/**
 * Index landing page
 * Includes a header with a call to action
 * Includes a small about section for the project
 */
import AboutProject from "../components/ui/AboutProject";
export default {
  components: {
    AboutProject,
  },
  data: function () {
    return {
      AboutItems: [
        {
          title: "Search",
          text: "Search Everything Food's extensive food options.",
        },
        {
          title: "Filter",
          text: "Filter your search results",
        },
      ],
    };
  },
};
</script>

<style>
</style>
