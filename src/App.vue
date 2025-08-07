<script setup>
import { ref } from 'vue'

const users = ref([])
const showUsers = ref(false)
const loading = ref(false)
const error = ref(null)

const loadUsers = async () => {
  loading.value = true
  error.value = null
  showUsers.value = true

  try {
    const res = await fetch('https://reqres.in/api/users', {
      headers: {
        'x-api-key': 'reqres-free-v1',
      },
    })
    const data = await res.json()
    users.value = data.data
  } catch (err) {
    error.value = 'Ошибка при загрузке пользователей'
  } finally {
    loading.value = false
  }
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

    <div v-if="showUsers" id="user-list" class="grid sm:grid-cols-2 md:grid-cols-3 gap-4 mt-6">
      <div
        v-for="user in users"
        :key="user.id"
        class="bg-white p-4 rounded shadow flex items-center space-x-4"
      >
        <img :src="user.avatar" :alt="user.first_name" class="w-16 h-16 rounded-full" />
        <div>
          <p class="text-lg font-semibold">{{ user.first_name }} {{ user.last_name }}</p>
          <p class="text-sm text-gray-500">{{ user.email }}</p>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped></style>
