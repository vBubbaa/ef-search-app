<template>
  <div class="h-full">
    <!-- Navbar -->
    <div class="bg-white w-full h-16 border-b-4 border-green-400">
      <div class="container p-4 h-full flex justify-between">
        <a href="" class="text-black hover:text-green-400 my-auto font-semibold"
          >Everything Food</a
        >
        <a href="" class="text-black hover:text-green-400 my-auto font-semibold"
          >Search</a
        >
      </div>
    </div>
    <!-- Main Content (Nuxt Pages) -->
    <div class="bg-white w-full h-full">
      <Nuxt />
    </div>
    <!-- Footer -->
    <div
      class="w-full border-t-4 border-green-400 flex justify-around p-4 flex-col sm:flex-row"
    >
      <!-- Contact Info. -->
      <div class="flex flex-col text-center py-2">
        <div
          class="font-semibold border-b-2 border-green-400 fit-content mx-auto"
        >
          Contact Me
        </div>
        <div>
          <a href="mailto:itylerjames@gmail.com" class="hover:text-green-400"
            >Email</a
          >
        </div>
        <div>
          <a
            href="https://github.com/vBubbaa"
            class="hover:text-green-400"
            target="_blank"
            >Github</a
          >
        </div>
        <div>
          <a
            href="https://www.linkedin.com/in/michael-james-60b81216b/"
            class="hover:text-green-400"
            target="_blank"
            >LinkedIn</a
          >
        </div>
      </div>
      <!-- This project requirements + API -->
      <div class="flex flex-col text-center py-2">
        <div
          class="font-semibold border-b-2 border-green-400 fit-content mx-auto"
        >
          This Project
        </div>
        <div>
          <a
            href="https://www.notion.so/Front-End-Code-Sample-659472f823264b659faa41cb1ec3bd64"
            class="hover:text-green-400"
            target="_blank"
            >Requirements</a
          >
        </div>
        <div>
          <a
            href="https://prod.everythingfood-api.com/api/v1/search/food_items?q=bagel"
            class="hover:text-green-400"
            target="_blank"
            >API</a
          >
        </div>
      </div>
      <!-- Additional Information -->
      <div class="flex flex-col text-center py-2">
        <div
          class="font-semibold border-b-2 border-green-400 fit-content mx-auto"
        >
          Extra
        </div>
        <div>
          <a href="" class="hover:text-green-400">About</a>
        </div>
        <div>
          <a
            href="https://coolors.co/000000-ffffff-68d391"
            class="hover:text-green-400"
            target="_blank"
            >Color Pallete</a
          >
        </div>
      </div>
    </div>
  </div>
</template>

<style>
html {
  font-family: "Source Sans Pro", -apple-system, BlinkMacSystemFont, "Segoe UI",
    Roboto, "Helvetica Neue", Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
  width: 100%;
  height: 100%;
}

body {
  width: 100%;
  height: 100%;
}

*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
}

/* Set default divs to 100% width and height */
#__nuxt,
#__layout {
  width: 100%;
  height: 100%;
}

.fit-content {
  width: fit-content;
}
</style>
