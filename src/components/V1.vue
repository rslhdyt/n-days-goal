<template>
    <div class="min-h-screen p-4 bg-white text-gray-800 flex flex-col">
      <h1 class="text-3xl font-bold mb-4 text-center">30 Days Mini Vlog</h1>
      <div class="grid grid-cols-7 gap-1 mb-2">
        <div v-for="day in days" :key="day" class="text-sm font-medium text-gray-500">{{ day }}</div>
      </div>
      <div class="grid grid-cols-7 gap-1">
        <div v-for="_ in startDayOfWeek" :key="'empty-' + _" class="aspect-square"></div>
        <button
          v-for="day in daysInMonth"
          :key="day"
          @click="toggleDay(day)"
          :class="[
            'aspect-square flex items-center justify-center text-sm font-medium border',
            checkedDays.includes(day)
              ? 'bg-green-500 text-white border-blue-600'
              : 'bg-white hover:bg-gray-100 border-gray-200',
            day === new Date().getDate() && selectedMonth === new Date().getMonth() && selectedYear === new Date().getFullYear()
              ? 'bg-blue-200'
              : '',
          ]"
        >
          <component :is="checkedDays.includes(day) ? 'Check' : 'span'">
            <Check v-if="checkedDays.includes(day)"></Check>
            <template v-else>{{ day }}</template>
          </component>
        </button>
      </div>
  
      {{ fetchedData }}
    </div>
  </template>
  
  <script setup>
  import { ref, computed, onMounted } from 'vue';
  import { Check } from 'lucide-vue-next';
  
  const checkedDays = ref([]);
  const selectedMonth = ref(new Date().getMonth());
  const selectedYear = ref(new Date().getFullYear());
  
  const daysInMonth = computed(() => {
    return new Date(selectedYear.value, selectedMonth.value + 1, 0).getDate();
  });
  
  const startDayOfWeek = computed(() => {
    return new Date(selectedYear.value, selectedMonth.value, 1).getDay();
  });
  const days = ['Sun', 'Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'];
  
  const toggleDay = (day) => {
    if (checkedDays.value.includes(day)) {
      checkedDays.value = checkedDays.value.filter(d => d !== day);
    } else {
      checkedDays.value.push(day);
    }
  };
  
  const fetchedData = ref(null);
  
  const fetchData = async () => {
    try {
      const url = 'https://script.google.com/macros/s/AKfycbzOoW6-I9k5kqFlJifPlexBEOtXg1htPeOhqdlzt0_N71Xlbxqu9efzM55iFN3ADzpS/exec'
      const response = await fetch(url, {
        method: 'GET'
      });
      fetchedData.value = await response.json();
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  };
  
  onMounted(() => {
    fetchData();
  });
  
  const formatDate = (dateString) => {
    const date = new Date(dateString);
    const year = date.getUTCFullYear();
    const month = String(date.getUTCMonth() + 1).padStart(2, '0');
    const day = String(date.getUTCDate()).padStart(2, '0');
    return `${year}-${month}-${day}`;
  };
  
  </script>