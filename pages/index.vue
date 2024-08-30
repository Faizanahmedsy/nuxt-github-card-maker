<script setup lang="ts">
import { ref } from "vue";
import { Input } from "@/components/ui/input/index.js";
import { Button } from "@/components/ui/button";
import { Alert, AlertDescription, AlertTitle } from "@/components/ui/alert";
import { UserX } from "lucide-react";

const username = ref("");
const userProfile = ref(null);
const error = ref("");
const userNotFound = ref(false);

const fetchGitHubProfile = async () => {
  if (!username.value) {
    error.value = "Please enter a GitHub username";
    userNotFound.value = false;
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
  } catch (err) {
    error.value = err.message;
    userProfile.value = null;
    userNotFound.value = false;
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
          <Alert v-if="error" variant="destructive" class="mt-4 max-w-[700px]">
            <AlertTitle>Error</AlertTitle>
            <AlertDescription>{{ error }}</AlertDescription>
          </Alert>
        </div>

        <div v-if="userNotFound" class="mt-8 max-w-[700px]">
          <Alert variant="warning">
            <UserX class="h-4 w-4" />
            <AlertTitle>User Not Found</AlertTitle>
            <AlertDescription>
              The GitHub user "{{ username }}" could not be found. Please check
              the username and try again.
            </AlertDescription>
          </Alert>
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
