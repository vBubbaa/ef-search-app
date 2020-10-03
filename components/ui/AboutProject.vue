<template>
  <div class="px-4 sm:px-8 py-8 text-center w-full">
    <div class="p-4">
      This project was made by
      <a
        href="https://www.linkedin.com/in/michael-james-60b81216b/"
        class="text-green-400"
      >
        Michael James</a
      >
      for the Everything Food's front end code submission.
    </div>
    <hr class="border-t-4 border-green-400" />
    <div class="py-2">
      The core objective is to create a NuxtJS SSR Universal application.
    </div>
    <div class="py-2">
      The application will be responsive and use TailwindCSS for styling.
    </div>
    <div class="py-2">
      This app will allow users to search the provided API for food items
    </div>
    <div class="py-2">
      There will be filters available to further filter the search results
      including [Brands, Package Sizes]
    </div>
    <div class="py-2">
      There will be sorting options including [ABBScore, and Alphabetical]
    </div>
  </div>
</template>

<script>
/**
 * About this project component show on the main index page.
 * Outlines the project goals, as well as what I used.
 */
export default {
  name: "aboutproject",
};
</script>

<style lang="scss" scoped>
</style>