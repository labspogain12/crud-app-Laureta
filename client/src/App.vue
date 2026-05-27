<script setup>
import { ref, onMounted } from 'vue'

const API = 'http://localhost:4000/api/items'
const items = ref([])
const form = ref({ name: '', description: '', price: '' })
const editId = ref(null)

async function load() {
  items.value = await fetch(API).then(r => r.json())
}

async function save() {
  if (editId.value) {
    await fetch(`${API}/${editId.value}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form.value)
    })
    editId.value = null
  } else {
    await fetch(API, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(form.value)
    })
  }
  form.value = { name: '', description: '', price: '' }
  load()
}

function startEdit(item) {
  editId.value = item.id
  form.value = { name: item.name, description: item.description, price: item.price }
}

async function remove(id) {
  await fetch(`${API}/${id}`, { method: 'DELETE' })
  load()
}

onMounted(load)
</script>

<template>
  <div class="wrapper">
    <div class="card">
      <h1>🛍️ Items List</h1>

      <form @submit.prevent="save" class="form">
        <input v-model="form.name" placeholder="Name" required />
        <input v-model="form.description" placeholder="Description" />
        <input v-model="form.price" placeholder="Price" type="number" step="0.01" />
        <button type="submit">{{ editId ? '✏️ Update' : '➕ Add' }}</button>
      </form>

      <table>
        <thead>
          <tr>
            <th>Name</th>
            <th>Description</th>
            <th>Price</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in items" :key="item.id">
            <td><strong>{{ item.name }}</strong></td>
            <td>{{ item.description }}</td>
            <td>₱{{ Number(item.price).toFixed(2) }}</td>
            <td>
              <button class="edit-btn" @click="startEdit(item)">✏️ Edit</button>
              <button class="delete-btn" @click="remove(item.id)">🗑️ Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style scoped>
.wrapper {
  min-height: 100vh;
  background: linear-gradient(135deg, #fce4ec, #e8f5e9, #e3f2fd);
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 40px 20px;
}

.card {
  background: #fff9fb;
  border-radius: 20px;
  padding: 30px;
  width: 100%;
  max-width: 700px;
  box-shadow: 0 8px 30px rgba(0,0,0,0.08);
}

h1 {
  text-align: center;
  color: #c2789b;
  margin-bottom: 24px;
  font-size: 1.8rem;
}

.form {
  display: flex;
  gap: 8px;
  flex-wrap: wrap;
  margin-bottom: 24px;
}

.form input {
  flex: 1;
  min-width: 120px;
  padding: 10px 14px;
  border: 2px solid #f8bbd0;
  border-radius: 10px;
  outline: none;
  font-size: 0.95rem;
  background: #fff;
  color: #555;
}

.form input:focus {
  border-color: #c2789b;
}

.form button {
  padding: 10px 20px;
  background: #c2789b;
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  font-size: 0.95rem;
  transition: background 0.2s;
}

.form button:hover {
  background: #a85c80;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead tr {
  background: #f8bbd0;
  color: #7b3f5e;
}

th, td {
  padding: 12px 16px;
  text-align: left;
  font-size: 0.9rem;
}

tbody tr:nth-child(even) {
  background: #fce4ec;
}

tbody tr:nth-child(odd) {
  background: #fff9fb;
}

.edit-btn {
  background: #b2dfdb;
  color: #00695c;
  border: none;
  padding: 6px 12px;
  border-radius: 8px;
  cursor: pointer;
  margin-right: 6px;
  font-size: 0.85rem;
}

.delete-btn {
  background: #ffcdd2;
  color: #c62828;
  border: none;
  padding: 6px 12px;
  border-radius: 8px;
  cursor: pointer;
  font-size: 0.85rem;
}

.edit-btn:hover { background: #80cbc4; }
.delete-btn:hover { background: #ef9a9a; }
</style>