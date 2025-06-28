<template>
  <div class="container">
    <h2>Tambah Post</h2>
    <form @submit.prevent="isEditing ? updatePost() : addPost()" class="post-form">
      <input v-model="newPost.title" placeholder="Judul" required />
      <input v-model="newPost.body" placeholder="Konten" required />
      <div class="button-group">
        <button type="submit">{{ isEditing ? 'Update' : 'Kirim' }}</button>
        <button v-if="isEditing" @click.prevent="cancelEdit" class="cancel-btn">Batal</button>
      </div>
    </form>

    <hr />

    <h2>Daftar Post</h2>
    <ul class="post-list">
      <li v-for="post in posts" :key="post.id" class="post-card">
        <div class="post-content">
          <strong>{{ post.title }}</strong>
          <p>{{ post.body }}</p>
        </div>
        <div class="actions">
          <button @click="editPost(post)" class="edit-btn">Edit</button>
          <button @click="deletePost(post.id)" class="delete-btn">Hapus</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'PostApp',
  data() {
    return {
      posts: [],
      newPost: {
        title: '',
        body: ''
      },
      isEditing: false,
      editId: null
    }
  },
  mounted() {
    this.loadPosts()
  },
  methods: {
    async loadPosts() {
      try {
        const response = await axios.get('http://localhost:3001/posts')
        this.posts = response.data
      } catch (error) {
        console.error('Gagal mengambil data:', error)
      }
    },
    async addPost() {
      try {
        await axios.post('http://localhost:3001/posts', this.newPost)
        this.resetForm()
        this.loadPosts()
      } catch (error) {
        console.error('Gagal menambahkan post:', error)
      }
    },
    editPost(post) {
      this.newPost.title = post.title
      this.newPost.body = post.body
      this.isEditing = true
      this.editId = post.id
    },
    async updatePost() {
      try {
        await axios.put(`http://localhost:3001/posts/${this.editId}`, this.newPost)
        this.resetForm()
        this.loadPosts()
      } catch (error) {
        console.error('Gagal mengupdate post:', error)
      }
    },
    async deletePost(id) {
      if (confirm('Yakin ingin menghapus post ini?')) {
        try {
          await axios.delete(`http://localhost:3001/posts/${id}`)
          this.loadPosts()
        } catch (error) {
          console.error('Gagal menghapus post:', error)
        }
      }
    },
    cancelEdit() {
      this.resetForm()
    },
    resetForm() {
      this.isEditing = false
      this.editId = null
      this.newPost.title = ''
      this.newPost.body = ''
    }
  }
}
</script>

<style>
body {
  background-color: #f0f8ff;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 700px;
  margin: 40px auto;
  background: #ffffff;
  padding: 20px 30px;
  border-radius: 12px;
  box-shadow: 0 8px 16px rgba(0,0,0,0.1);
}

h2 {
  color: #2c3e50;
  margin-bottom: 15px;
}

.post-form input {
  display: block;
  width: 100%;
  margin-bottom: 10px;
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 14px;
}

.button-group {
  display: flex;
  gap: 10px;
}

.post-form button {
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.3s ease;
}

.post-form button:hover {
  background-color: #45a049;
}

.cancel-btn {
  background-color: #9e9e9e;
}

.cancel-btn:hover {
  background-color: #7e7e7e;
}

.post-list {
  list-style-type: none;
  padding: 0;
}

.post-card {
  background: #e0f7fa;
  border-radius: 10px;
  padding: 15px 20px;
  margin-bottom: 15px;
  box-shadow: 0 4px 8px rgba(0,0,0,0.05);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.post-content p {
  margin: 5px 0 0;
  color: #555;
}

.actions button {
  padding: 8px 12px;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  margin-left: 5px;
  font-size: 14px;
}

.edit-btn {
  background-color: #fbc02d;
  color: #333;
}

.edit-btn:hover {
  background-color: #f9a825;
}

.delete-btn {
  background-color: #e57373;
  color: white;
}

.delete-btn:hover {
  background-color: #ef5350;
}

hr {
  border: 0;
  height: 1px;
  background: #ddd;
  margin: 20px 0;
}
</style>
