<script setup lang="ts">
import { ref } from "vue";
import { Input } from "@/components/ui/input/index.js";
import { Button } from "@/components/ui/button";

const username = ref("");
const userProfile = ref(null);
const error = ref("");

const fetchGitHubProfile = async () => {
  if (!username.value) {
    error.value = "Please enter a GitHub username";
    return;
  }

  try {
    const response = await fetch(
      `https://api.github.com/users/${username.value}`
    );
    if (!response.ok) {
      throw new Error("User not found");
    }
    userProfile.value = await response.json();
    error.value = "";
  } catch (err: any) {
    error.value = err.message;
    userProfile.value = null;
  }
};
</script>

<template>
  <section class="">
    <div
      class="flex gap-6 justify-center items-center min-h-[calc(100dvh-100px)]"
    >
      <div class="w-1/5 border-2 min-h-[calc(100dvh-100px)] py-32 px-6">
        <p class="text-2xl font-bold">
          Make a beautiful personal card with your GitHub profile
        </p>
      </div>

      <div class="w-4/5 min-h-[calc(100dvh-100px)] flex py-32 px-10 flex-col">
        <div>
          <div class="flex gap-6">
            <span
              class="bg-blue-600 px-4 text-white flex justify-center items-center"
              >1</span
            >
            <h2 class="text-3xl font-semibold">
              Please enter your GitHub username
            </h2>
          </div>
          <div class="max-w-[700px] my-6">
            <Input
              v-model="username"
              type="text"
              placeholder="GitHub Username"
            />
            <Button @click="fetchGitHubProfile" class="mt-4">
              Fetch Profile
            </Button>
          </div>
          <p v-if="error" class="text-red-500 mt-2">{{ error }}</p>
        </div>

        <div v-if="userProfile" class="mt-8">
          <h3 class="text-2xl font-semibold mb-4">GitHub Profile Card</h3>
          <div class="bg-white shadow-md rounded-lg p-6 max-w-[700px]">
            <img
              :src="userProfile.avatar_url"
              alt="Profile Picture"
              class="w-24 h-24 rounded-full mb-4"
            />
            <h4 class="text-xl font-bold">{{ userProfile.name }}</h4>
            <p class="text-gray-600">@{{ userProfile.login }}</p>
            <p class="mt-2">{{ userProfile.bio }}</p>
            <div class="mt-4 flex gap-4">
              <p><strong>Followers:</strong> {{ userProfile.followers }}</p>
              <p><strong>Following:</strong> {{ userProfile.following }}</p>
              <p>
                <strong>Public Repos:</strong> {{ userProfile.public_repos }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
