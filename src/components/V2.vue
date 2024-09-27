<template>
  <div class="container mx-auto sm:max-w-md">
    <h1 class="text-3xl font-bold mb-4 text-gray-800 text-center pt-10">30-Days Speaking Challenge</h1>
    <hr class="border-t-teal-400 border-t-4 mb-5">

    <p class="text-gray-500 mb-5 text-justify">
      Welcome to my 30-day speaking challenge! This page tracks my journey to improve communication skills through daily mini vlogs. With AI-generated topics, I'm pushing myself to create and share content regularly. Join me as I learn, grow, and become a more confident communicator!
    </p>

    <div class="px-3">
      <div class="w-full bg-gray-300 rounded-full h-2.5 mb-4">
        <div class="bg-gradient-to-r from-yellow-500 to-green-500 h-2.5 rounded-full" :style="{ width: `${completedPercentage}%` }"></div>
      </div>

      <p class="text-lg mb-6 text-teal-600 text-center">
        {{ completedPercentage }}% Completed
      </p>
    </div>

    <div class="flex flex-col space-y-5">
      <div v-if="!progresses.length" class="flex justify-center items-center h-32">
        <svg class="animate-spin h-8 w-8 text-blue-600" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
          <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
          <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4zm2 5.291A7.962 7.962 0 014 12H0c0 3.042 1.135 5.824 3 7.938l3-2.647z"></path>
        </svg>
      </div>

      <div v-for="progress in progresses" class="rounded overflow-hidden shadow-lg bg-white cursor-pointer" @click="selectedProgress = progress">
        <template v-if="progress.video_url">
          <img class="w-full" :src="progress.video_thumbnail_url" alt="Video Thumbnail">
        </template>
        <template v-else>
          <div class="w-full h-48 bg-gray-100 flex items-center justify-center">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-24 w-24 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
            </svg>
          </div>
        </template>
        <div class="px-6 py-4 border border-t-teal-400 border-t-4">
          <div class="font-bold text-xl mb-2 text-gray-800">Day #{{ progress.day }} - {{ progress.objective }}</div>
          <p class="text-gray-600 text-sm" v-if="progress.uploaded_at">
            <span class="mr-2">
              <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 inline-block" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
              </svg>
            </span>
            {{ progress.uploaded_at }}
          </p>
        </div>
      </div>
    </div>

    <footer class="mt-10 py-4 border-t border-gray-200">
      <div class="container mx-auto text-center">
        <p class="text-gray-600">
          © 2024 30-Days Speaking Challenge. Created by 
          <a href="https://rslhdyt.dev" target="_blank" rel="noopener noreferrer" class="text-teal-600 hover:text-teal-800 transition duration-300">
            Risal Hidayat
          </a>
        </p>
      </div>
    </footer>

    <!-- Modal -->
    <div v-if="selectedProgress" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
      <div class="bg-white rounded-lg p-6 max-w-lg w-full mx-4">
        <div class="flex justify-between items-center mb-4">
          <h2 class="text-2xl font-bold text-gray-800">Day #{{ selectedProgress.day }}</h2>
          <button @click="closeModal" class="text-gray-600 hover:text-gray-800">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
          </button>
        </div>
        <div class="mb-4">
          <div v-if="selectedProgress.video_url" class="aspect-video mb-4 w-full">
            <iframe
              :src="selectedProgress.video_url"
              frameborder="0"
              allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
              allowfullscreen
              class="w-full h-full"
            ></iframe>
          </div>
        </div>
        <h3 class="text-xl font-semibold mb-2 text-gray-800">{{ selectedProgress.objective }}</h3>
        <p class="text-gray-600 mb-3" v-if="selectedProgress.uploaded_at">
          <span class="mr-2">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 inline-block" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </span>
          {{ selectedProgress.uploaded_at }}
        </p>
        <hr>
        <p class="text-gray-600 text-sm mt-3 text-justify">
          {{ selectedProgress.description || 'No description available.' }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onBeforeMount } from 'vue'

const completedPercentage = ref(0)
const progresses = ref([])
const selectedProgress = ref(null)

const fetchProgresses = async () => {
  try {
    const response = await fetch('https://script.google.com/macros/s/AKfycbzOoW6-I9k5kqFlJifPlexBEOtXg1htPeOhqdlzt0_N71Xlbxqu9efzM55iFN3ADzpS/exec');
    const data = await response.json();

    const items = data.map(item => ({
      day: item.day,
      objective: item.objective,
      video_url: item.video_url || '',
      uploaded_at: item.uploaded_at ? new Date(item.uploaded_at).toLocaleDateString('en-US', { day: 'numeric', month: 'long', year: 'numeric' }) : null,
      video_thumbnail_url: item.video_thumbnail_url || `https://via.placeholder.com/320x180.png?text=Day+${item.day}+Thumbnail`,
      description: item.description
    }));

    // Add progresses one by one with a delay
    const addProgressWithDelay = (index) => {
      if (index < items.length) {
        setTimeout(() => {
          progresses.value.push(items[index]);
          addProgressWithDelay(index + 1);
        }, 100); // 100ms delay between each item
      }
    };

    // Start adding progresses
    addProgressWithDelay(0);

    completedPercentage.value = Math.round((items.filter(p => p.video_url).length / items.length) * 100);
  } catch (error) {
    console.error('Error fetching progresses:', error);
  }
};

const closeModal = () => {
  selectedProgress.value = null;
};

onBeforeMount(() => {
  fetchProgresses();
})
</script>