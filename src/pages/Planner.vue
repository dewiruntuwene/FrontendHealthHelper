<script setup lang="ts">
import Navbar from "../components/Navbar.vue";
import { ref, onMounted } from "vue";
import axios from "axios";

// State untuk menampung data dari API
const plannerData = ref<any[]>([]);

// Function untuk memformat teks
const formatText = (text: string): string => {
  // Mengganti ### dengan <h4> untuk membuat header
  let formattedText = text.replace(/###\s([^\n]+)/g, "<h4>$1</h4>");
  
  // Mengganti - dengan <li> untuk membuat list item
  formattedText = formattedText.replace(/-\s([^\n]+)/g, "<ul><li>$1</li></ul>");
  
  // Mengganti line breaks \n dengan <br> untuk menjaga pemisahan antar paragraf
  formattedText = formattedText.replace(/\n/g, "<br>");

  return formattedText;
};

// Fetch data dari API saat komponen di-mount
const fetchPlannerData = async () => {
  const apiUrl = import.meta.env.VITE_API_URL;
  const token = localStorage.getItem("token");

  try {
    const response = await axios.get(`${apiUrl}/getplanner`, {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });
    plannerData.value = response.data;
  } catch (error) {
    console.error("Error fetching planner data:", error);
  }
};

// Panggil fetchPlannerData saat komponen di-mount
onMounted(() => {
  fetchPlannerData();
});
</script>

<template>
  <Navbar />
  <div class="mt-24 ml-8">
    <div v-for="planner in plannerData" :key="planner.id" class="bg-orange-50 border rounded-lg border-red-700 p-6 mb-4">
      <!-- Card Header -->
      <h3 class="text-xl font-bold mb-2">Planner ID: {{ planner.id }}</h3>

      <!-- Food Plan -->
      <div class="mb-4">
        <h4 class="text-md font-semibold mb-4">Food Plan</h4>
        <!-- Apply formatText function to food_plan -->
        <div v-html="formatText(planner.food_plan)" class="text-sm mb-0"></div>
      </div>

      <!-- Exercise Plan -->
      <div>
        <h4 class="text-md font-semibold mb-4">Exercise Plan</h4>
        <!-- Apply formatText function to exercise_plan -->
        <div v-html="formatText(planner.exercise_plan)" class="text-sm"></div>
      </div>
    </div>
  </div>
</template>
