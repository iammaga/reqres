<script setup>
import { ref } from 'vue'

const users = ref([])
const selectedUser = ref(null)
const loading = ref(false)
const error = ref(null)
const showUsers = ref(false)

const loadUsers = async () => {
  loading.value = true
  error.value = null
  showUsers.value = true
  selectedUser.value = null

  try {
    const res = await fetch('https://reqres.in/api/users', {
      headers: { 'x-api-key': 'reqres-free-v1' },
    })
    if (!res.ok) throw new Error('Ошибка загрузки пользователей')
    const data = await res.json()
    users.value = data.data
  } catch (e) {
    error.value = e.message
  } finally {
    loading.value = false
  }
}

const fetchUserById = async (id) => {
  loading.value = true
  error.value = null

  try {
    const res = await fetch(`https://reqres.in/api/users/${id}`, {
      headers: { 'x-api-key': 'reqres-free-v1' },
    })
    if (!res.ok) throw new Error('Ошибка загрузки пользователя')
    const data = await res.json()
    selectedUser.value = data.data
  } catch (e) {
    error.value = e.message
  } finally {
    loading.value = false
  }
}

const resetView = () => {
  selectedUser.value = null
  error.value = null
}
</script>

<template>
  <header class="bg-white shadow">
    <nav class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4 flex justify-between items-center">
      <a href="#" class="text-xl font-bold text-gray-800">Reqres.in</a>

      <ul class="flex space-x-6">
        <li>
          <a href="#" class="text-gray-700 hover:text-blue-600" @click.prevent="loadUsers">
            Пользователи
          </a>
        </li>
      </ul>

      <div>
        <a href="#" class="px-4 py-2 text-sm bg-blue-600 text-white rounded hover:bg-blue-700">
          Создать
        </a>
      </div>
    </nav>
  </header>

  <main class="max-w-5xl mx-auto p-4">
    <div v-if="loading" class="text-gray-600 mt-4">Загрузка...</div>
    <div v-if="error" class="text-red-600 mt-4">{{ error }}</div>

    <div
      v-if="showUsers && !selectedUser"
      id="user-list"
      class="grid sm:grid-cols-2 md:grid-cols-3 gap-4 mt-6"
    >
      <div
        v-for="user in users"
        :key="user.id"
        class="bg-white p-4 rounded shadow flex items-center space-x-4 cursor-pointer hover:bg-blue-50"
        @click="fetchUserById(user.id)"
      >
        <img :src="user.avatar" :alt="user.first_name" class="w-16 h-16 rounded-full" />
        <div>
          <p class="text-lg font-semibold">{{ user.first_name }} {{ user.last_name }}</p>
          <p class="text-sm text-gray-500">{{ user.email }}</p>
        </div>
      </div>
    </div>

    <section
      v-if="selectedUser"
      class="bg-white p-6 rounded shadow flex items-center space-x-6 w-full"
    >
      <img
        :src="selectedUser.avatar"
        :alt="selectedUser.first_name"
        class="w-24 h-24 rounded-full"
      />
      <div>
        <h2 class="text-2xl font-bold mb-1">
          {{ selectedUser.first_name }} {{ selectedUser.last_name }}
        </h2>
        <p class="text-gray-700 mb-1">Email: {{ selectedUser.email }}</p>
        <!-- <button
          @click="resetView"
          class="mt-4 px-4 py-2 bg-gray-400 text-white rounded hover:bg-gray-500"
        >
          Назад
        </button> -->
      </div>
    </section>
  </main>
</template>

<style scoped></style>
