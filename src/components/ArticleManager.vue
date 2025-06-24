<template>
  <div class="container">
    <form @submit.prevent="save" class="form">
      <input type="text" v-model="form.title" placeholder="Judul Artikel" /><br />
      <textarea v-model="form.content" placeholder="Isi Artikel"></textarea><br />
      <button type="submit">Save</button>
    </form>

    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <h3>{{ article.title }}</h3>
        <p>{{ article.content }}</p>
        <div class="btn-group">
          <button @click="edit(article)">Edit</button>
          <button @click="remove(article.id)" class="delete-btn">Delete</button>
        </div>
      </li>
    </ul>

    <button @click="load" class="load-btn">Load Data</button>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'
import axios from 'axios'

const form = reactive({
  id: null,
  title: '',
  content: ''
})

const articles = ref([])

async function load() {
  try {
    const response = await axios.get('http://localhost:3000/articles')
    articles.value = response.data
  } catch (error) {
    console.error('Gagal memuat data:', error)
  }
}

async function save() {
  try {
    if (form.id) {
      await axios.put(`http://localhost:3000/articles/${form.id}`, form)
      const index = articles.value.findIndex(a => a.id === form.id)
      articles.value[index] = { ...form }
    } else {
      const response = await axios.post('http://localhost:3000/articles', form)
      articles.value.push(response.data)
    }

    form.id = null
    form.title = ''
    form.content = ''
  } catch (error) {
    console.error('Gagal menyimpan:', error)
  }
}

function edit(article) {
  form.id = article.id
  form.title = article.title
  form.content = article.content
}

async function remove(id) {
  try {
    await axios.delete(`http://localhost:3000/articles/${id}`)
    articles.value = articles.value.filter(article => article.id !== id)
  } catch (error) {
    console.error('Gagal menghapus:', error)
  }
}

onMounted(load)
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 30px auto;
  padding: 20px;
  background: #f7f9fa;
  border-radius: 8px;
  box-shadow: 0 0 10px #ccc;
}

.form input,
.form textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #bbb;
  border-radius: 4px;
  font-size: 14px;
}

.form button {
  padding: 8px 16px;
  background: #007bff;
  border: none;
  color: white;
  border-radius: 4px;
  cursor: pointer;
}

.form button:hover {
  background: #0056b3;
}

.article-list {
  margin-top: 20px;
  list-style: none;
  padding: 0;
}

.article-item {
  background: #fff;
  padding: 15px;
  margin-bottom: 15px;
  border-left: 4px solid #007bff;
  border-radius: 4px;
}

.article-item h3 {
  margin: 0;
  font-size: 18px;
  color: #333;
}

.article-item p {
  color: #666;
  font-size: 14px;
}

.btn-group {
  margin-top: 10px;
}

.btn-group button {
  margin-right: 10px;
  padding: 6px 12px;
  border: none;
  border-radius: 4px;
  font-size: 13px;
  cursor: pointer;
}

.btn-group button:hover {
  opacity: 0.9;
}

.btn-group .delete-btn {
  background: #dc3545;
  color: white;
}

.btn-group button:first-child {
  background: #ffc107;
  color: white;
}

.load-btn {
  margin-top: 20px;
  padding: 8px 16px;
  background: #28a745;
  border: none;
  color: white;
  border-radius: 4px;
  cursor: pointer;
}

.load-btn:hover {
  background: #218838;
}
</style>
