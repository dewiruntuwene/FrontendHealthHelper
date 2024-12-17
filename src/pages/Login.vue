<script setup lang="ts">
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import { JwtPayload, jwtDecode } from "jwt-decode";

const router = useRouter();
const email = ref("");
const password = ref("");
const errorMessage = ref(""); // Empty error message initially

interface CustomJwtPayload extends JwtPayload {
  role: string;
}

const apiUrl = import.meta.env.VITE_API_URL;

// const navigate = (url: string) => {
//   window.location.href = url;
// };

const urlParams = new URLSearchParams(window.location.search);
console.log("this url:", urlParams);
//const token: string = urlParams.get("token") || "";


// Fungsi untuk mengarahkan pengguna ke Google untuk otentikasi


// Fungsi untuk memeriksa token di URL setelah redirect
const checkToken = () => {
  const urlParams = new URLSearchParams(window.location.search);
  const token = urlParams.get("token");

  console.log("Token ditemukan di URL:", token);

  if (token) {
    try {
      const decodedToken: CustomJwtPayload = jwtDecode(token);
      console.log("Decoded Token:", decodedToken);

      if (decodedToken.exp && Date.now() >= decodedToken.exp * 1000) {
        console.error("Token sudah kedaluwarsa.");
        return;
      }

      // Simpan token ke localStorage
      localStorage.setItem("token", token);
      alert("Token berhasil disimpan.");
    } catch (error) {
      console.error("Gagal mendekode token:", error);
    }
  } else {
    console.error("Tidak ada token yang ditemukan di URL.");
  }
};

window.onload = checkToken;


// Fungsi login
const login = async () => {
  try {
    const response = await axios.post(`${apiUrl}/login`, {
      email: email.value,
      password: password.value,
    });

    const token = response.data.token;
    console.log("Login berhasil, token:", token);

    // Simpan token di localStorage
    localStorage.setItem("token", token);
    axios.defaults.headers.common["Authorization"] = `Bearer ${token}`;

    // Redirect ke halaman /Chat
    router.push("/Chat");
    alert("Login berhasil!");
  } catch (error) {
    console.error("Error:", error);
    if (axios.isAxiosError(error) && error.response) {
      const status = error.response.status;
      switch (status) {
        case 400:
          errorMessage.value = "Password salah.";
          alert(errorMessage.value);
          break;
        case 404:
          errorMessage.value = "Email tidak ditemukan.";
          alert(errorMessage.value);
          break;
        default:
          errorMessage.value = "Terjadi kesalahan pada server.";
          alert(errorMessage.value);
      }
    } else {
      errorMessage.value = "Terjadi kesalahan pada aplikasi.";
      alert(errorMessage.value);
    }
  }
};

</script>

<template>
  <div
    class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700 items-center justify-content-center"
  >
    <form class="space-y-6" action="#" @submit.prevent="login">
      <h5 class="text-xl font-medium text-gray-900 dark:text-white">
        Sign in to our platform
      </h5>
      <div>
        <label
          for="email"
          class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
          >Your email</label
        >
        <input
          type="email"
          name="email"
          id="email"
          v-model="email"
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white"
          placeholder="name@company.com"
          required
        />
      </div>
      <div>
        <label
          for="password"
          class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
          >Your password</label
        >
        <input
          type="password"
          name="password"
          id="password"
          placeholder="••••••••"
          v-model="password"
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white"
          required
        />
      </div>

      <button
        type="submit"
        class="w-full text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
      >
        Login to your account
      </button>
      <div class="text-sm font-medium text-gray-500 dark:text-gray-300">
        Not registered?
        <router-link to="/register" class="underline">Register</router-link>
      </div>
    </form>
    <!-- <button
      @click="auth"
      aria-label="Sign in with Google"
      class="flex items-center mt-4 bg-white border border-button-border-light rounded-full p-0.5 pr-4"
    >
      <div
        class="flex items-center justify-center bg-white w-9 h-9 rounded-full"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          class="w-5 h-5"
        >
          <title>Sign in with Google</title>
          <desc>Google G Logo</desc>
          <path
            d="M22.56 12.25c0-.78-.07-1.53-.2-2.25H12v4.26h5.92c-.26 1.37-1.04 2.53-2.21 3.31v2.77h3.57c2.08-1.92 3.28-4.74 3.28-8.09z"
            class="fill-google-logo-blue"
          ></path>
          <path
            d="M12 23c2.97 0 5.46-.98 7.28-2.66l-3.57-2.77c-.98.66-2.23 1.06-3.71 1.06-2.86 0-5.29-1.93-6.16-4.53H2.18v2.84C3.99 20.53 7.7 23 12 23z"
            class="fill-google-logo-green"
          ></path>
          <path
            d="M5.84 14.09c-.22-.66-.35-1.36-.35-2.09s.13-1.43.35-2.09V7.07H2.18C1.43 8.55 1 10.22 1 12s.43 3.45 1.18 4.93l2.85-2.22.81-.62z"
            class="fill-google-logo-yellow"
          ></path>
          <path
            d="M12 5.38c1.62 0 3.06.56 4.21 1.64l3.15-3.15C17.45 2.09 14.97 1 12 1 7.7 1 3.99 3.47 2.18 7.07l3.66 2.84c.87-2.6 3.3-4.53 6.16-4.53z"
            class="fill-google-logo-red"
          ></path>
        </svg>
      </div>
      <span class="text-sm text-google-text-gray tracking-wider"
        >Sign in with Google</span
      >
    </button> -->
  </div>

  <!-- <div class="bg-gray-100 flex items-center justify-center h-screen">
    <div class="bg-white p-8 rounded-lg shadow-md w-80">
      <h2 class="text-2xl font-semibold mb-4">Klinik Inventory System</h2>
      <h3 class="text-lg text-gray-600 mb-6 text-center">Login</h3>
      <form @submit.prevent="login">
        <div class="mb-4">
          <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
          <input type="text" id="email" v-model="email" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-300 focus:ring focus:ring-blue-200 focus:ring-opacity-50">
        </div>
        <div class="mb-4">
          <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
          <input type="password" id="password" v-model="password" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-300 focus:ring focus:ring-blue-200 focus:ring-opacity-50">
        </div>
        <div class="mt-4 text-gray-600 text-center">Belum Punya Account Silahkan <router-link to="/register" class="underline">Register</router-link>.</div>
        <div class="flex flex-col items-center justify-between">
          <button type="submit" class="mt-5 bg-blue-500 text-white py-2 px-4 rounded focus:outline-none focus:bg-blue-600 hover:bg-blue-600">Login</button>
        </div>
      </form>
      <div>
        <button @click="auth" class="mt-4 bg-red-500 text-white py-2 px-4 rounded focus:outline-none focus:bg-red-600 hover:bg-red-600">Login with Google</button>
      </div>
    </div>
  </div>  -->
</template>

<style>
body,
html {
  height: 100%;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

.card {
  width: 400px;
  padding: 20px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
  border-radius: 10px;
  background-color: #fff;
  text-align: center;
}
</style>
