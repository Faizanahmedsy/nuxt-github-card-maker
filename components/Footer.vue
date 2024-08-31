<script setup></script>

<template>
  <footer class="bg-teal-900 text-white py-4">
    <div class="container mx-auto flex justify-between px-10 items-center">
      <div>
        Edit on
        <a
          href="https://github.com/Faizanahmedsy/nuxt-github-card-maker"
          target="_blank"
          class="text-blue-200"
        >
          GitHub</a
        >
      </div>
      <!-- <p class="text-sm">
        &copy;
        <span v-if="new Date().getFullYear()"></span>
        {{ new Date().getFullYear() }} Nuxt Website. All rights reserved. Your
        Website. All rights reserved.
      </p> -->
      <p>
        Built with Nuxt by
        <a
          href="https://faizansaiyed.vercel.app"
          target="_blank"
          class="text-blue-200"
          >Faizanahmed</a
        >
      </p>
    </div>
  </footer>
</template>

<script>
export default {
  name: "Footer",
};
</script>

<style scoped>
/* Add any custom styles for the footer here */
.footer {
  background-color: #333;
  color: #fff;
  padding: 20px;
  text-align: center;
}

.footer p {
  font-size: 14px;
  margin: 0;
}
</style>
