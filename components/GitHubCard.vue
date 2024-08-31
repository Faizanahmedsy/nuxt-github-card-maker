<script setup lang="ts">
import { ref } from "vue";
import { Button } from "@/components/ui/button";
import { Download } from "lucide-react";
import html2canvas from "html2canvas";
import jsPDF from "jspdf";

const props = defineProps<{
  userProfile: any;
}>();

const cardRef = ref(null);

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
  pdf.save(`${props.userProfile.login}_github_card.pdf`);
};
</script>

<template>
  <div
    class="flex flex-col lg:flex-row gap-4 lg:gap-6 items-start lg:items-center mb-6"
  >
    <span
      class="bg-emerald-600 px-4 text-white flex justify-center items-center rounded-full lg:rounded"
      >2</span
    >
    <h2 class="text-2xl lg:text-3xl font-semibold">
      Download your card as a PDF
    </h2>
  </div>
  <div
    ref="cardRef"
    class="bg-gradient-to-br from-blue-600 to-blue-800 rounded-xl shadow-2xl overflow-hidden max-w-[700px] w-full"
  >
    <div class="p-6 lg:p-8">
      <div class="flex flex-col lg:flex-row items-center lg:space-x-6">
        <div>
          <img
            :src="userProfile.avatar_url"
            alt="Profile Picture"
            class="w-24 h-24 lg:w-32 lg:h-32 rounded-full border-4 border-white shadow-lg mb-4 lg:mb-0"
          />
        </div>
        <div class="text-center lg:text-left">
          <h3 class="text-2xl lg:text-3xl font-bold text-white">
            {{ userProfile.name }}
          </h3>
          <p class="text-lg lg:text-xl text-blue-200">
            @{{ userProfile.login }}
          </p>
          <!-- // if userProfile.company is not empty -->
          <p
            v-if="userProfile.company"
            class="text-lg lg:text-xl text-blue-200"
          >
            {{ userProfile.company }}
          </p>
        </div>
      </div>
      <p class="mt-6 text-base lg:text-lg text-white text-center lg:text-left">
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
  </div>
  <div
    class="bg-teal-100 px-6 py-4 lg:px-8 flex flex-col lg:flex-row justify-between items-center space-y-4 lg:space-y-0 max-w-[700px] rounded-xl my-5"
  >
    <p class="text-gray-600 text-sm lg:text-base">
      Created at:
      {{ new Date(userProfile.created_at).toLocaleDateString() }}
    </p>
    <Button
      @click="downloadAsPDF"
      class="bg-teal-500 hover:bg-teal-600 download-button w-full lg:w-auto"
    >
      <Download class="h-5 w-5 mr-2" />
      Download PDF
    </Button>
  </div>
</template>
