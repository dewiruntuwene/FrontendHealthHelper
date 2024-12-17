<template>
  <div>
    <nav class="bg-white shadow-lg fixed top-0 w-full sticky-navbar z-50">
      <div class="container mx-auto px-4">
        <div class="flex items-center justify-between h-16">
          <!-- Hamburger Button -->
          <div class="p-4 sm:hidden">
            <button
              @click="toggleSidebar"
              id="hamburgerButton"
              class="text-blue-500 focus:outline-none"
            >
              <svg
                class="w-6 h-6"
                fill="none"
                stroke="currentColor"
                viewBox="0 0 24 24"
                xmlns="http://www.w3.org/2000/svg"
              >
                <path
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M4 6h16M4 12h16M4 18h16"
                ></path>
              </svg>
            </button>
          </div>

          <!-- Sidebar -->
          <div
            :class="[
              'fixed top-0 left-0 h-full w-64 bg-white  transform transition-transform',
              isSidebarOpen ? 'translate-x-0' : '-translate-x-full',
            ]"
          >
            <div class="p-4">
              <button
                @click="toggleSidebar"
                class="text-red-500 focus:outline-none mb-4"
              >
                <svg
                  class="w-6 h-6"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M6 18L18 6M6 6l12 12"
                  ></path>
                </svg>
              </button>
              <nav aria-label="breadcrumb">
                <ol
                  class="bg-white p-2 rounded-md shadow-md flex flex-col space-y-2"
                >
                  <li>
                    <button
                      class="text-blue-500 hover:underline"
                      @click="logout"
                    >
                      Logout
                    </button>
                  </li>
                </ol>
              </nav>
            </div>
          </div>

          <div class="flex-shrink-0">
            <router-link to="/" class="text-xl font-bold text-gray-800"
              >Health Checker</router-link
            >
          </div>

          <!-- Input Search -->
          <!-- <div class="row mt-3">
          <div class="col">
            <div class="input-group mb-3">
              <input
                v-model="searchQuery"
                type="text"
                class="form-control"
                placeholder="Cari Barang..."
                aria-label="Cari"
                aria-describedby="basic-addon1"
                @input="searchBarang"
              />
              <div class="input-group-prepend">
                <span class="input-group-text" id="basic-addon1">
                  <b-icon-search></b-icon-search>
                </span>
              </div>
            </div>
          </div>
        </div> -->

          <!-- Menampilkan daftar barang -->
          <!-- <div v-if="searchResults.length > 0">
          <h2>Hasil Pencarian:</h2>
          <ul>
            <li v-for="barang in searchResults" :key="barang.kode_barang">{{ barang.nama_barang }}</li>
          </ul>
        </div>
        <div v-else>
          <p>Tidak ada barang ditemukan.</p>
        </div> -->

          <div class="hidden md:block">
            <div class="ml-10 flex items-baseline space-x-4">

              <router-link
                to="/Planner"
                class="text-gray-800 hover:text-gray-600"
                >Dashboard</router-link
              >

              <router-link
                to="/Chat"
                class="text-gray-800 hover:text-gray-600"
                >Konsultasi</router-link
              >

              <router-link
                to="/Profile"
                class="text-gray-800 hover:text-gray-600"
                >Account</router-link
              >

              <div class="flex-row relative">
                <a
                  href="#"
                  class="inline-block text-sm px-4 py-2 leading-none border-black rounded text-black border-white hover:border-transparent hover:text-natural-900 hover:bg-blue mt-4 lg:mt-0"
                  @click="logout"
                  >Logout</a
                >
              </div>
            </div>
          </div>

          <!-- component -->
          <div class="flex items-center">
            <div class="relative py-2">
            </div>
          </div>
        </div>
      </div>
    </nav>
  </div>
</template>

<script setup lang="ts">
import axios from "axios";
import { ref } from "vue";

const isSidebarOpen = ref(false);
const apiUrl = import.meta.env.VITE_API_URL;

const toggleSidebar = () => {
  isSidebarOpen.value = !isSidebarOpen.value;
};


const logout = async () => {
  const token = localStorage.getItem("token");
  console.log("Token from localStorage:", token); // Debugging tambahan
  if (!token) {
    console.error("No token found");
    return;
  }
  try {
    const response = await axios.delete(`${apiUrl}/logout`, {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });

    if (response.status === 200) {
      // Memeriksa respons status
      localStorage.removeItem("token");
      // Redirect to login page or perform any other action
      window.location.href = "/";
    } else {
      console.error("Logout failed", response.data);
      // Handle logout failure
    }
  } catch (error) {
    console.error("Logout error:", error);
    // Handle error
  }
};



</script>
