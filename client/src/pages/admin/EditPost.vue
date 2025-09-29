<template>
  <div>
    <RouterLink to="/admin/dashboard">&larr; Back</RouterLink>

    <div class="admin-title">
      <h2>View / Edit Post</h2>

      <!-- Delete Post -->
      <form @submit.prevent="deletePost">
        <input type="submit" value="Delete" class="btn-delete btn" />
      </form>
    </div>

    <!-- Edit Post -->
    <form @submit.prevent="updatePost">
      <label for="title"><b>Title</b></label>
      <input
        type="text"
        id="title"
        v-model="title"
        placeholder="Post Title"
        required
      />

      <label for="body"><b>Content</b></label>
      <textarea
        id="body"
        v-model="body"
        cols="50"
        rows="10"
        required
      ></textarea>

      <input type="submit" value="Update" class="btn" />
    </form>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const title = ref('')
const body = ref('')
const route = useRoute()
const router = useRouter()
const postId = route.params.id

// Load the post when the component mounts
onMounted(async () => {
  try {
    const res = await fetch(`/api/admin/posts/${postId}`) 
    if (!res.ok) throw new Error('Failed to fetch post')
    const json = await res.json()

    // adjust depending on backend response shape
    title.value = json.title || json.data?.title || ''
    body.value = json.body || json.data?.body || ''
  } catch (err) {
    console.error('Error loading post:', err)
  }
})

// Update Post
async function updatePost() {
  try {
    const res = await fetch(`/api/admin/edit-post/${postId}`, {
      method: 'PUT',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ title: title.value, body: body.value }),
    })

    if (res.ok) {
      router.push('/admin/dashboard')
    } else {
      console.error('Failed to update post')
    }
  } catch (err) {
    console.error('Error updating post:', err)
  }
}

// Delete Post
async function deletePost() {
  if (!confirm('Are you sure you want to delete this post?')) return
  try {
    const res = await fetch(`/api/admin/delete-post/${postId}`, {
      method: 'DELETE',
    })

    if (res.ok) {
      router.push('/admin/dashboard')
    } else {
      console.error('Failed to delete post')
    }
  } catch (err) {
    console.error('Error deleting post:', err)
  }
}
</script>
