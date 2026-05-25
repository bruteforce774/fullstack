<script setup>
import { ref, onMounted } from 'vue'

const users = ref([])
const newName = ref('')
const editingId = ref(null)
const editingName = ref('')

async function fetchUsers() {
  const response = await fetch('http://localhost:3000/users')
  users.value = await response.json()
}

async function createUser() {
  await fetch('http://localhost:3000/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: newName.value }),
  })
  newName.value = ''
  await fetchUsers()
}

async function deleteUser(id) {
  await fetch(`http://localhost:3000/users/${id}`, { method: 'DELETE' })
  await fetchUsers()
}

function startEdit(user) {
  editingId.value = user.id
  editingName.value = user.name
}

async function saveEdit(id) {
  await fetch(`http://localhost:3000/users/${id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ name: editingName.value }),
  })
  editingId.value = null
  await fetchUsers()
}

onMounted(fetchUsers)
</script>

<template>
  <main>
    <h1>Users</h1>
    <form @submit.prevent="createUser">
      <input v-model="newName" placeholder="Name" />
      <button type="submit">Add</button>
    </form>
    <ul>
      <li v-for="user in users" :key="user.id">
        <template v-if="editingId === user.id">
          <input v-model="editingName" />
          <button @click="saveEdit(user.id)">Save</button>
          <button @click="editingId = null">Cancel</button>
        </template>
        <template v-else>
          {{ user.name }}
          <button @click="startEdit(user)">Edit</button>
          <button @click="deleteUser(user.id)">Delete</button>
        </template>
      </li>
    </ul>
  </main>
</template>
