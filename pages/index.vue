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

  // Clone the card element to remove the download button
  const cardClone = cardRef.value.cloneNode(true);
  const downloadButton = cardClone.querySelector(".download-button");
  if (downloadButton) {
    downloadButton.remove();
  }

  // Temporarily append the clone to the document body
  document.body.appendChild(cardClone);

  // Capture the card as an image, including the avatar
  const canvas = await html2canvas(cardClone, {
    useCORS: true,
    allowTaint: true,
  });

  // Remove the clone from the document body
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

        <div
          v-if="userProfile"
          ref="cardRef"
          class="mt-8 bg-gradient-to-br from-blue-500 to-purple-600 rounded-xl shadow-2xl overflow-hidden max-w-[700px]"
        >
          <div class="p-8">
            <div class="flex items-center space-x-6">
              <img
                :src="userProfile.avatar_url"
                alt="Profile Picture"
                class="w-32 h-32 rounded-full border-4 border-white shadow-lg"
              />
              <div>
                <h3 class="text-3xl font-bold text-white">
                  {{ userProfile.name }}
                </h3>
                <p class="text-xl text-blue-200">@{{ userProfile.login }}</p>
              </div>
            </div>
            <p class="mt-6 text-lg text-white">{{ userProfile.bio }}</p>
            <div class="mt-8 flex justify-between text-blue-100">
              <div class="text-center">
                <p class="text-3xl font-bold">{{ userProfile.followers }}</p>
                <p class="text-sm uppercase">Followers</p>
              </div>
              <div class="text-center">
                <p class="text-3xl font-bold">{{ userProfile.following }}</p>
                <p class="text-sm uppercase">Following</p>
              </div>
              <div class="text-center">
                <p class="text-3xl font-bold">{{ userProfile.public_repos }}</p>
                <p class="text-sm uppercase">Repos</p>
              </div>
            </div>
          </div>
          <div class="bg-white px-8 py-4 flex justify-between items-center">
            <p class="text-gray-600">
              Created at:
              {{ new Date(userProfile.created_at).toLocaleDateString() }}
            </p>
            <Button
              @click="downloadAsPDF"
              class="bg-green-500 hover:bg-green-600 download-button"
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
