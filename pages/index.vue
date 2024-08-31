<script setup lang="ts">
import { ref } from "vue";
import { Input } from "@/components/ui/input/index.js";
import { Button } from "@/components/ui/button";
import { Alert, AlertTitle, AlertDescription } from "@/components/ui/alert";
import { UserX } from "lucide-react";
import GitHubCard from "~/components/GitHubCard.vue";
import NavbarV3 from "~/components/NavbarV3.vue";
import { Github, Loader2 } from "lucide-vue-next";

const username = ref("");
const userProfile = ref(null);
const error = ref("");
const userNotFound = ref(false);
const loading = ref(false);

const fetchGitHubProfile = async () => {
  loading.value = true;

  if (!username.value) {
    error.value = "Please enter a GitHub username";
    userNotFound.value = false;
    loading.value = false;
    return;
  }

  try {
    const response = await fetch(
      `https://api.github.com/users/${username.value}`
    );
    if (response.status === 404) {
      userNotFound.value = true;
      userProfile.value = null;
      error.value = "";
      return;
    }
    if (!response.ok) {
      throw new Error("An error occurred while fetching the profile");
    }
    userProfile.value = await response.json();
    error.value = "";
    userNotFound.value = false;
  } catch (err: any) {
    error.value = err.message;
    userProfile.value = null;
    userNotFound.value = false;
  }
  loading.value = false;
};
</script>

<template>
  <NavbarV3 />
  <section class="bg-gradient-to-t from-teal-200 to-teal-50 px-10">
    <div class="flex flex-col lg:flex-row min-h-screen">
      <div
        class="w-full lg:w-1/5 border-b-2 lg:border-b-0 lg:border-r-2 border-gray-200 p-6 lg:py-10"
      >
        <p class="text-xl lg:text-xl font-bold text-center lg:text-left">
          Make a beautiful personal card with your GitHub profile
        </p>

        <div class="bg-teal-50 text-teal-800 p-4 rounded-2xl my-4">
          <p>Note:</p>
          <hr />
          <p class="text-sm lg:text-base text-center lg:text-left">
            I made this project only to learn nuxt, it is not a real product.
            However, you can use it to create a printable cards with your GitHub
            profile. I will be adding better card designs in future.
          </p>
        </div>

        <div
          class="bg-teal-300 text-sm text-teal-900 px-4 py-2 rounded-2xl my-4 cursor-pointer text-center"
        >
          <a
            href="https://github.com/Faizanahmedsy/nuxt-github-card-maker"
            class="flex items-center justify-center"
            target="_blank"
          >
            <Github class="h-4 w-4 mr-2" />
            Contribute on GitHub
          </a>
        </div>
      </div>

      <div class="w-full lg:w-4/5 p-6 lg:p-10 flex flex-col">
        <div>
          <div
            class="flex flex-col lg:flex-row gap-4 lg:gap-6 items-start lg:items-center mb-6"
          >
            <span
              class="bg-emerald-600 px-4 text-white flex justify-center items-center rounded-full lg:rounded"
              >1</span
            >
            <h2 class="text-2xl lg:text-3xl font-semibold">
              Please enter your GitHub username
            </h2>
          </div>
          <div class="max-w-[700px] mb-6">
            <Input
              v-model="username"
              type="text"
              placeholder="GitHub Username"
              class="mb-4"
            />
            <Button @click="fetchGitHubProfile" class="w-full lg:w-auto">
              <template v-if="loading">
                <Loader2 class="w-4 h-4 mr-2 animate-spin w-full" />
                Please wait...
              </template>
              <template v-else> Fetch Profile </template>
            </Button>
          </div>
          <Alert v-if="error" variant="destructive" class="mb-6 max-w-[700px]">
            <AlertTitle>Error</AlertTitle>
            <AlertDescription>{{ error }}</AlertDescription>
          </Alert>
        </div>

        <div v-if="userNotFound" class="mb-6 max-w-[700px]">
          <Alert variant="destructive">
            <UserX class="h-4 w-4" />
            <AlertTitle>User Not Found</AlertTitle>
            <AlertDescription>
              The GitHub user "{{ username }}" could not be found. Please check
              the username and try again.
            </AlertDescription>
          </Alert>
        </div>
        <GitHubCard v-if="userProfile" :userProfile="userProfile" />
      </div>
    </div>
  </section>
</template>
