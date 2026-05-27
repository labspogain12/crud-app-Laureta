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
  <div class="page">
    <div class="container">

      <div class="header">
        <div class="header-line"></div>
        <h1>✦ Items Catalogue ✦</h1>
        <div class="header-line"></div>
      </div>

      <form @submit.prevent="save" class="form">
        <input v-model="form.name" placeholder="Item Name" required />
        <input v-model="form.description" placeholder="Description" />
        <input v-model="form.price" placeholder="Price (₱)" type="number" step="0.01" />
        <button type="submit">{{ editId ? '✎ Update' : '✚ Add Item' }}</button>
      </form>

      <div class="divider">❧</div>

      <table>
        <thead>
          <tr>
            <th>#</th>
            <th>Item Name</th>
            <th>Description</th>
            <th>Price</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in items" :key="item.id">
            <td class="num">{{ index + 1 }}</td>
            <td><strong>{{ item.name }}</strong></td>
            <td class="muted">{{ item.description }}</td>
            <td class="price">₱{{ Number(item.price).toFixed(2) }}</td>
            <td class="actions">
              <button class="edit-btn" @click="startEdit(item)">✎ Edit</button>
              <button class="delete-btn" @click="remove(item.id)">✕ Delete</button>
            </td>
          </tr>
          <tr v-if="items.length === 0">
            <td colspan="5" class="empty">— No items yet. Add one above. —</td>
          </tr>
        </tbody>
      </table>

      <div class="footer">✦ {{ items.length }} item{{ items.length !== 1 ? 's' : '' }} in catalogue ✦</div>
    </div>
  </div>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;600&family=EB+Garamond:wght@400;500&display=swap');

* { box-sizing: border-box; margin: 0; padding: 0; }

.page {
  min-height: 100vh;
  background-color: #f5efe0;
  background-image:
    repeating-linear-gradient(
      0deg,
      transparent,
      transparent 39px,
      rgba(180, 150, 100, 0.12) 39px,
      rgba(180, 150, 100, 0.12) 40px
    );
  display: flex;
  justify-content: center;
  align-items: flex-start;
  padding: 40px 24px;
  font-family: 'EB Garamond', Georgia, serif;
}

.container {
  background: #fdf8ee;
  border: 2px solid #c9a96e;
  border-radius: 4px;
  width: 100%;
  max-width: 1100px;
  padding: 36px 48px;
  box-shadow: 4px 4px 0px #c9a96e, 8px 8px 0px rgba(180,150,100,0.2);
}

.header {
  display: flex;
  align-items: center;
  gap: 16px;
  margin-bottom: 28px;
}

.header-line {
  flex: 1;
  height: 2px;
  background: repeating-linear-gradient(
    90deg,
    #c9a96e 0px, #c9a96e 6px,
    transparent 6px, transparent 10px
  );
}

h1 {
  font-family: 'Playfair Display', serif;
  font-size: 1.8rem;
  font-weight: 600;
  color: #6b4c2a;
  white-space: nowrap;
  letter-spacing: 2px;
}

.form {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
  margin-bottom: 20px;
}

.form input {
  flex: 1;
  min-width: 140px;
  padding: 10px 14px;
  border: 1.5px solid #c9a96e;
  border-radius: 2px;
  background: #fffdf5;
  font-family: 'EB Garamond', serif;
  font-size: 1rem;
  color: #4a3520;
  outline: none;
  transition: border-color 0.2s;
}

.form input:focus {
  border-color: #8b5e2a;
}

.form input::placeholder {
  color: #b89d7a;
}

.form button {
  padding: 10px 24px;
  background: #6b4c2a;
  color: #fdf8ee;
  border: 1.5px solid #4a3520;
  border-radius: 2px;
  font-family: 'Playfair Display', serif;
  font-size: 1rem;
  cursor: pointer;
  letter-spacing: 1px;
  transition: background 0.2s;
}

.form button:hover {
  background: #4a3520;
}

.divider {
  text-align: center;
  font-size: 1.4rem;
  color: #c9a96e;
  margin: 4px 0 20px;
  letter-spacing: 8px;
}

table {
  width: 100%;
  border-collapse: collapse;
  font-size: 1rem;
}

thead tr {
  background: #6b4c2a;
  color: #fdf8ee;
}

th {
  padding: 12px 16px;
  text-align: left;
  font-family: 'Playfair Display', serif;
  font-weight: 600;
  font-size: 0.95rem;
  letter-spacing: 1px;
}

td {
  padding: 11px 16px;
  color: #4a3520;
  border-bottom: 1px solid #e8d5b0;
}

tbody tr:nth-child(even) {
  background: #faf3e0;
}

tbody tr:hover {
  background: #f5e8c8;
}

.num {
  color: #b89d7a;
  font-size: 0.9rem;
  width: 40px;
}

.muted {
  color: #7a5c38;
}

.price {
  font-family: 'Playfair Display', serif;
  font-weight: 600;
  color: #5a3e1b;
}

.actions {
  white-space: nowrap;
}

.edit-btn {
  background: #d4e8d0;
  color: #2d5a27;
  border: 1px solid #a8c8a0;
  padding: 5px 14px;
  border-radius: 2px;
  cursor: pointer;
  font-family: 'EB Garamond', serif;
  font-size: 0.95rem;
  margin-right: 6px;
  transition: background 0.2s;
}

.delete-btn {
  background: #f0d5d5;
  color: #7a2020;
  border: 1px solid #d4a0a0;
  padding: 5px 14px;
  border-radius: 2px;
  cursor: pointer;
  font-family: 'EB Garamond', serif;
  font-size: 0.95rem;
  transition: background 0.2s;
}

.edit-btn:hover { background: #b8d8b0; }
.delete-btn:hover { background: #e0b0b0; }

.empty {
  text-align: center;
  color: #b89d7a;
  font-style: italic;
  padding: 24px;
}

.footer {
  text-align: center;
  margin-top: 24px;
  color: #b89d7a;
  font-size: 0.9rem;
  letter-spacing: 3px;
}
</style>
