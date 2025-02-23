<script setup>
import { ref } from 'vue';
import axios from 'axios';

const email = ref('');
const password = ref('');
const user = ref(null);
const token = ref(localStorage.getItem('token') || '');
const errorMessage = ref('');

const login = async () => {
  try {
    const response = await axios.post('http://127.0.0.1:8000/api/login', {
      email: email.value,
      password: password.value
    });

    token.value = response.data.token;
    user.value = response.data.user;
    localStorage.setItem('token', token.value);
  } catch (error) {
    errorMessage.value = 'Login gagal. Periksa kembali email dan password!';
  }
};

const getUser = async () => {
  try {
    const response = await axios.get('http://127.0.0.1:8000/api/user', {
      headers: { Authorization: `Bearer ${token.value}` }
    });
    user.value = response.data;
  } catch (error) {
    errorMessage.value = 'Gagal mengambil data user';
  }
};

const logout = () => {
  localStorage.removeItem('token');
  user.value = null;
  token.value = '';
};
</script>

<template>
  <div class="max-w-md mx-auto mt-10 p-5 border rounded-lg shadow">
    <h2 class="text-xl font-bold mb-4">Login</h2>
    <input type="email" v-model="email" placeholder="Email" class="w-full p-2 border mb-2" />
    <input type="password" v-model="password" placeholder="Password" class="w-full p-2 border mb-2" />
    <button @click="login" class="w-full bg-blue-500 text-white p-2">Login</button>

    <p v-if="errorMessage" class="text-red-500 mt-2">{{ errorMessage }}</p>

    <div v-if="user" class="mt-4">
      <p><strong>Nama:</strong> {{ user.name }}</p>
      <p><strong>Email:</strong> {{ user.email }}</p>
      <button @click="logout" class="mt-2 bg-red-500 text-white p-2">Logout</button>
    </div>
  </div>
</template>

<style scoped>
input {
  border-radius: 4px;
}
button {
  border-radius: 4px;
}
</style>
