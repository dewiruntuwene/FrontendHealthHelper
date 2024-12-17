<script setup lang="ts">
import { ref } from 'vue';
import axios from 'axios';
import Navbar from "../components/Navbar.vue";

// Define reactive variables for messages
const messages = ref([
  { sender: 'bot', text: 'Hello! How can I assist you today?' }
]);

const apiUrl = import.meta.env.VITE_API_URL;

// Define input message
const userMessage = ref('');

// Method to send message
const sendMessage = async () => {
  if (userMessage.value.trim()) {
    // Add user message to chat
    messages.value.push({ sender: 'user', text: userMessage.value });

    // Call the backend API to get the bot's response
    try {
    const token = localStorage.getItem("token");
    const response = await axios.post(
        `${apiUrl}/createdataai`,
        { // Data payload
          messages: [
            { role: 'user', content: userMessage.value },
          ],
        },
        { // Config header
          headers: {
            Authorization: `Bearer ${token}`,
          },
        }
      );


      console.log(token)
      const botMessage = response.data.output; // Assuming the response contains the output message from the backend
      messages.value.push({ sender: 'bot', text: botMessage });
    } catch (error) {
      messages.value.push({ sender: 'bot', text: 'Sorry, something went wrong!' });
    }
    userMessage.value = ''; // Clear input field
  }
};
</script>

<template>
  <Navbar />
  <body class="h-screen overflow-hidden flex items-center justify-center" style="background: #edf2f7;">
    <div class="flex h-screen antialiased text-gray-800 mt-32 mb-24">
      <div class="flex flex-col flex-auto h-full p-6">
        <div class="flex flex-col flex-auto flex-shrink-0 rounded-2xl bg-gray-100 h-full p-4">
          <div class="flex flex-col h-full overflow-x-auto mb-24">
            <div class="flex flex-col h-full">
              <div class="grid grid-cols-12 gap-y-2">
                <!-- Display messages dynamically -->
                <div v-for="(message, index) in messages" :key="index" :class="{
                  'col-start-1 col-end-8 p-3 rounded-lg': message.sender === 'user',
                  'col-start-6 col-end-13 p-3 rounded-lg': message.sender === 'bot'
                }">
                  <div class="flex flex-row items-center">
                    <div
                      :class="{
                        'flex items-center justify-center h-10 w-10 rounded-full bg-indigo-500 flex-shrink-0': message.sender === 'user',
                        'flex items-center justify-center h-10 w-10 rounded-full bg-indigo-200 flex-shrink-0': message.sender === 'bot'
                      }">
                      <span v-if="message.sender === 'user'">U</span>
                      <span v-if="message.sender === 'bot'">B</span>
                    </div>
                    <div
                      :class="{
                        'relative ml-3 text-sm bg-white py-2 px-4 shadow rounded-xl': message.sender === 'user',
                        'relative mr-3 text-sm bg-indigo-100 py-2 px-4 shadow rounded-xl': message.sender === 'bot'
                      }">
                      <div>{{ message.text }}</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- Chat input -->
          <div class="flex flex-row items-center mt-4">
            <input
              v-model="userMessage"
              type="text"
              class="flex-auto p-3 border border-gray-300 rounded-l-xl"
              placeholder="Type your message..."
            />
            <button
              @click="sendMessage"
              class="bg-indigo-500 text-white p-3 rounded-r-xl">
              Send
            </button>
          </div>
        </div>
      </div>
    </div>
  </body>
</template>

<style scoped>
/* Customize your styling if needed */
</style>
