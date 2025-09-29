<template>
  <div>
    <RouterLink to="/admin/dashboard">&larr; Back</RouterLink>

    <div class="admin-title">
      <h2>Add New Post</h2>
    </div>

    <form @submit.prevent="submitPost">
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

      <input type="submit" value="Add" class="btn" />
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const title = ref('')
const body = ref('')
const router = useRouter()

async function submitPost() {
  try {
    const res = await fetch('/api/admin/add-post', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ title: title.value, body: body.value }),
      credentials: 'include'
    })

    if (res.ok) {
      router.push('/admin/dashboard') // redirect after success
    } else {
      console.error('Failed to add post')
    }
  } catch (err) {
    console.error('Error submitting post:', err)
  }
}
</script>
