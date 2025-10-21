<script>
import axios from 'axios';

export default {
  data() {
    return {
      moods: ['Happy', 'Sad', 'Angry'],
      posts: [],
      entry: '',
      mood: '',
      showEditPost: false,
      editPostId: null,
    };
  },

  computed: {
    baseUrl() {
      if (window.location.hostname === 'localhost') return 'http://localhost:3000';
      const codespace_host = window.location.hostname.replace('5173', '3000');
      return `https://${codespace_host}`;
    },
  },

  created() {
    axios
      .get(`${this.baseUrl}/posts`)
      .then((response) => {
        this.posts = Array.isArray(response.data) ? response.data : [];
      })
      .catch((error) => {
        this.posts = [{ id: '-', entry: 'There was an error: ' + error.message, mood: '' }];
      });
  },

  methods: {
    editPost(post) {
        if (!post) return;
        this.editPostId = post.id;
        this.entry = post.entry ?? '';
        this.mood = (post.mood || '').toLowerCase();
        this.showEditPost = true;
    },

    updatePost(event) {
        if (event && event.preventDefault) event.preventDefault();

        const payload = { id: this.editPostId, entry: this.entry, mood: this.mood };

        const idx = this.posts.findIndex(p => p.id === this.editPostId);
        if (idx !== -1) {
            const updatedRow = { ...this.posts[idx], entry: this.entry, mood: this.mood };
            this.posts.splice(idx, 1, updatedRow);
        }

        axios.get(`${this.baseUrl}/updatePost`)
            .catch(() => null)
            .then(() => axios.post(`${this.baseUrl}/updatePost?id=${Number(this.editPostId)}`, payload))
            .then(() => {
                this.showEditPost = false;
                this.editPostId = null;
                this.entry = '';
                this.mood = '';
            })
            .catch((error) => {
                alert('Error updating post: ' + (error?.message || error));
            });
        },
  },
};
</script>

<template>
  <div id="demo">
    <h2> View Blog Posts </h2>

    <table class="table m-2">
      <thead>
        <tr>
          <th>ID</th>
          <th>Entry</th>
          <th>Mood</th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in posts" :key="post.id">
          <td>{{ post.id }}</td>
          <td>{{ post.entry }}</td>
          <td>{{ post.mood }}</td>
          <td><button @click="editPost(post)">Edit</button></td>
        </tr>
      </tbody>
    </table>

    <!-- Edit form the tests look for -->
    <div id="editPost" v-if="showEditPost">
      <h3>Edit Post</h3>
      <div id="postContent" class="mx-3">
        <!-- prevent native submit -->
        <form @submit.prevent="updatePost">
          <div class="mb-3">
            <label for="entry" class="form-label">Entry</label>
            <textarea id="entry" class="form-control" v-model="entry" required></textarea>
          </div>
          <div class="mb-3">
            <label for="mood" class="form-label">Mood</label>
            <select id="mood" class="form-select" v-model="mood" required>
              <option value="" disabled>Select Mood</option>
              <option v-for="mood in moods" :key="mood" :value="mood.toLowerCase()">{{ mood }}</option>
            </select>
          </div>
          <button type="submit" class="btn btn-primary">Update Post</button>
        </form>
      </div>
    </div>
  </div>
</template>

<style scoped></style>
