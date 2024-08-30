<script setup lang="ts">
import { ref } from "vue";
import { Input } from "@/components/ui/input/index.js";
import { Button } from "@/components/ui/button";
import { Alert, AlertTitle, AlertDescription } from "@/components/ui/alert";
import { UserX, Download } from "lucide-react";
import html2canvas from "html2canvas";
import jsPDF from "jspdf";

const username = ref("");
const userProfile = ref(null);
const error = ref("");
const userNotFound = ref(false);
const cardRef = ref(null);

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

const downloadAsPDF = async () => {
  if (!cardRef.value) return;

  const cardClone = cardRef.value.cloneNode(true);
  const downloadButton = cardClone.querySelector(".download-button");
  if (downloadButton) {
    downloadButton.remove();
  }

  document.body.appendChild(cardClone);

  const canvas = await html2canvas(cardClone, {
    useCORS: true,
    allowTaint: true,
  });

  document.body.removeChild(cardClone);

  const imgData = canvas.toDataURL("image/png");
  const pdf = new jsPDF({
    orientation: "landscape",
    unit: "px",
    format: [canvas.width, canvas.height],
  });

  pdf.addImage(imgData, "PNG", 0, 0, canvas.width, canvas.height);
  pdf.save(`${userProfile.value.login}_github_card.pdf`);
};
</script>

<template>
  <div class="flex justify-between items-center py-4 px-32">
    <h2 class="font-semibold">Nuxt Github Card Creator</h2>
    <div class="text-sm">
      Built with Nuxt by
      <a
        href="https://faizansaiyed.vercel.app"
        target="_blank"
        class="text-blue-500"
        >Faizanahmed</a
      >
    </div>
  </div>
  <section class="bg-blue-50 px-28">
    <div class="flex flex-col lg:flex-row min-h-screen">
      <div
        class="w-full lg:w-1/5 border-b-2 lg:border-b-0 lg:border-r-2 border-gray-200 p-6 lg:py-10"
      >
        <p class="text-xl lg:text-xl font-bold text-center lg:text-left">
          Make a beautiful personal card with your GitHub profile
        </p>
      </div>

      <div class="w-full lg:w-4/5 p-6 lg:p-10 flex flex-col">
        <div>
          <div
            class="flex flex-col lg:flex-row gap-4 lg:gap-6 items-start lg:items-center mb-6"
          >
            <span
              class="bg-blue-600 px-4 py-2 text-white flex justify-center items-center rounded-full lg:rounded"
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
              Fetch Profile
            </Button>
          </div>
          <Alert v-if="error" variant="destructive" class="mb-6 max-w-[700px]">
            <AlertTitle>Error</AlertTitle>
            <AlertDescription>{{ error }}</AlertDescription>
          </Alert>
        </div>

        <div v-if="userNotFound" class="mb-6 max-w-[700px]">
          <Alert variant="warning">
            <UserX class="h-4 w-4" />
            <AlertTitle>User Not Found</AlertTitle>
            <AlertDescription>
              The GitHub user "{{ username }}" could not be found. Please check
              the username and try again.
            </AlertDescription>
          </Alert>
        </div>

        <div
          v-if="userProfile"
          ref="cardRef"
          class="bg-gradient-to-br from-blue-500 to-purple-600 rounded-xl shadow-2xl overflow-hidden max-w-[700px] w-full"
        >
          <div class="p-6 lg:p-8">
            <div
              class="flex flex-col lg:flex-row items-center lg:items-start lg:space-x-6"
            >
              <img
                :src="userProfile.avatar_url"
                alt="Profile Picture"
                class="w-24 h-24 lg:w-32 lg:h-32 rounded-full border-4 border-white shadow-lg mb-4 lg:mb-0"
              />
              <div class="text-center lg:text-left">
                <h3 class="text-2xl lg:text-3xl font-bold text-white">
                  {{ userProfile.name }}
                </h3>
                <p class="text-lg lg:text-xl text-blue-200">
                  @{{ userProfile.login }}
                </p>
              </div>
            </div>
            <p
              class="mt-6 text-base lg:text-lg text-white text-center lg:text-left"
            >
              {{ userProfile.bio }}
            </p>
            <div class="mt-8 flex justify-between text-blue-100">
              <div class="text-center">
                <p class="text-2xl lg:text-3xl font-bold">
                  {{ userProfile.followers }}
                </p>
                <p class="text-xs lg:text-sm uppercase">Followers</p>
              </div>
              <div class="text-center">
                <p class="text-2xl lg:text-3xl font-bold">
                  {{ userProfile.following }}
                </p>
                <p class="text-xs lg:text-sm uppercase">Following</p>
              </div>
              <div class="text-center">
                <p class="text-2xl lg:text-3xl font-bold">
                  {{ userProfile.public_repos }}
                </p>
                <p class="text-xs lg:text-sm uppercase">Repos</p>
              </div>
            </div>
          </div>
          <div
            class="bg-white px-6 py-4 lg:px-8 flex flex-col lg:flex-row justify-between items-center space-y-4 lg:space-y-0"
          >
            <p class="text-gray-600 text-sm lg:text-base">
              Created at:
              {{ new Date(userProfile.created_at).toLocaleDateString() }}
            </p>
            <Button
              @click="downloadAsPDF"
              class="bg-green-500 hover:bg-green-600 download-button w-full lg:w-auto"
            >
              <Download class="h-5 w-5 mr-2" />
              Download PDF
            </Button>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>
