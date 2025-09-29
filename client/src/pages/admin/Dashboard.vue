<template>
  <div>
    <div class="admin-title">
      <h2>Posts</h2>

      <!-- Show + Add New button only if logged in -->
      <RouterLink 
        v-if="isLoggedIn" 
        to="/admin/add-post" 
        class="button">
        + Add New
      </RouterLink>

      <!-- Show message if not logged in -->
      <p v-else class="login-required-message">
        You must be logged in to see posts.
      </p>
    </div>

    <!-- Show posts only if logged in and there are posts -->
    <ul class="admin-posts" v-if="isLoggedIn && posts.length">
      <li v-for="post in posts" :key="post._id">
        <RouterLink :to="`/post/${post._id}`">
          {{ post.title }} &nearr;
        </RouterLink>

        <div class="admin-post-controls">
          <RouterLink :to="`/admin/edit-post/${post._id}`" class="btn">Edit</RouterLink>

          <button @click="deletePost(post._id)" class="btn-delete btn">
            Delete
          </button>
        </div>
      </li>
    </ul>

    <!-- Show a placeholder if no posts -->
    <p v-if="isLoggedIn && posts.length === 0">No posts available.</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const posts = ref([])
const isLoggedIn = ref(false)
const router = useRouter()

// Check if the user is logged in before mounting the dashboard
onMounted(async () => {
  await checkAuthStatus()
  if (isLoggedIn.value) {
    await fetchPosts() // Fetch posts if logged in
  }
})

// Function to check login status
async function checkAuthStatus() {
  try {
    const res = await fetch('/api/admin/check-auth', {
      method: 'GET',
      credentials: 'include',
    })
    const json = await res.json()
    isLoggedIn.value = json.loggedIn
  } catch (err) {
    console.error('Auth check failed:', err)
    isLoggedIn.value = false
  }
}

// Function to fetch posts
async function fetchPosts() {
  try {
    const res = await fetch('/api/admin/dashboard', {
      credentials: 'include',
    })
    const json = await res.json()
    posts.value = json.posts
  } catch (err) {
    console.error('Error loading posts:', err)
  }
}

// Function to delete a post
async function deletePost(id) {
  if (!confirm('Are you sure you want to delete this post?')) return
  try {
    const res = await fetch(`/api/admin/delete-post/${id}`, {
      method: 'DELETE',
      credentials: 'include',
    })
    if (res.ok) {
      posts.value = posts.value.filter(p => p._id !== id)
    } else {
      console.error('Delete failed')
    }
  } catch (err) {
    console.error('Error deleting post:', err)
  }
}
</script>

<style scoped>
/* Styling for message when not logged in */
.login-required-message {
  color: #f44336;
  font-weight: bold;
  margin-top: 10px;
}

/* Styling for admin posts section */
.admin-posts {
  list-style: none;
  padding: 0;
}

.admin-posts li {
  margin-bottom: 15px;
}

/* Ensuring footer doesn't shift up */
body {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

footer {
  margin-top: auto;
}
</style>
