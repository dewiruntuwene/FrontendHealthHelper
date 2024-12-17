<script setup lang="ts">
import { ref } from "vue";
import axios from "axios";

const username = ref("");
const email = ref("");
const password = ref("");
const confpassword = ref("");
const registrationSuccess = ref(false);
const userExists = ref(false); // Variabel untuk menampilkan pemberitahuan jika pengguna sudah terdaftar

const apiUrl = import.meta.env.VITE_API_URL;

const register = async () => {
  try {
    const data = {
      username: username.value,
      email: email.value,
      password: password.value,
      confpassword: confpassword.value,
    };

    // Kirim permintaan pendaftaran ke server
    const response = await axios.post(
      `${apiUrl}/register`,
      data
    );
    console.log("Response:", response.data);

    // Tampilkan pemberitahuan bahwa registrasi berhasil
    registrationSuccess.value = true;
    userExists.value = false; // Reset variabel userExists

    // Tambahkan pemberitahuan bahwa registrasi berhasil
    alert("Registrasi berhasil! Silakan login.");
  } catch (error) {
    console.error("Error:", error);
    alert("Tejadi Kesalahan Saat Registrasi");
  }
};
</script>

<template>
  <div
    class="w-full max-w-sm p-4 bg-white border border-gray-200 rounded-lg shadow sm:p-6 md:p-8 dark:bg-gray-800 dark:border-gray-700 items-center justify-content-center"
  >
    <form class="space-y-6" action="#" @submit.prevent="register">
      <h5 class="text-xl font-medium text-gray-900 dark:text-white">
        Register
      </h5>
      <div>
        <label
          for="username"
          class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
          >Username</label
        >
        <input
          type="text"
          name="username"
          id="email"
          v-model="username"
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white"
          placeholder="name"
          required
        />
      </div>
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
      <div>
        <label
          for="confpassword"
          class="block mb-2 text-sm font-medium text-gray-900 dark:text-white"
          >Confirm your password</label
        >
        <input
          type="password"
          name="password"
          id="confpassword"
          placeholder="••••••••"
          v-model="confpassword"
          class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5 dark:bg-gray-600 dark:border-gray-500 dark:placeholder-gray-400 dark:text-white"
          required
        />
      </div>

      <button
        type="submit"
        class="w-full text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800"
      >
        Register
      </button>
      <div class="text-sm font-medium text-gray-500 dark:text-gray-300">
        Have an account?
        <router-link to="/login" class="underline">Login</router-link>
      </div>
      <!-- Pemberitahuan registrasi berhasil -->
      <div v-if="registrationSuccess" class="mt-4 text-green-600">
        Registrasi berhasil! Silakan
        <router-link to="/login" class="underline">login</router-link>.
      </div>
      <!-- Pemberitahuan jika pengguna sudah terdaftar -->
      <div v-if="userExists" class="mt-4 text-red-600">
        Pengguna dengan email atau username ini sudah terdaftar.
      </div>
    </form>
  </div>

  <!-- <div class="bg-gray-100 min-h-screen flex items-center justify-center">
    <div class="bg-white p-8 rounded-lg shadow-md w-80">
      <h2 class="text-2xl font-semibold mb-4 text-center">Register</h2>
      <form @submit.prevent="register">
        <div class="mb-4">
          <label for="username" class="block text-sm font-medium text-gray-700">Username</label>
          <input type="text" id="username" v-model="username" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-300 focus:ring focus:ring-blue-200 focus:ring-opacity-50">
        </div>
        <div class="mb-4">
          <label for="email" class="block text-sm font-medium text-gray-700">Email</label>
          <input type="email" id="email" v-model="email" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-300 focus:ring focus:ring-blue-200 focus:ring-opacity-50">
        </div>
        <div class="mb-4">
          <label for="password" class="block text-sm font-medium text-gray-700">Password</label>
          <input type="password" id="password" v-model="password" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-300 focus:ring focus:ring-blue-200 focus:ring-opacity-50">
        </div>
        <div class="mb-4">
          <label for="confpassword" class="block text-sm font-medium text-gray-700">Konfirmasi Password</label>
          <input type="password" id="confpassword" v-model="confpassword" class="mt-1 block w-full rounded-md border-gray-300 shadow-sm focus:border-blue-300 focus:ring focus:ring-blue-200 focus:ring-opacity-50">
        </div>
        <div  class="mt-4 text-gray-600 text-center">Sudah Punya Account Silahkan <router-link to="/login" class="underline">Login</router-link>.</div>
        <div class="flex flex-col items-center justify-between">
          <button type="submit" class="mt-5 items-center bg-blue-500 text-white py-2 px-4 rounded focus:outline-none focus:bg-blue-600 hover:bg-blue-600">Daftar</button>
        </div>
    
        <div v-if="registrationSuccess" class="mt-4 text-green-600">Registrasi berhasil! Silakan <router-link to="/login" class="underline">login</router-link>.</div>
       
        <div v-if="userExists" class="mt-4 text-red-600">Pengguna dengan email atau username ini sudah terdaftar.</div>
      </form>
    </div>  
  </div> -->
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
