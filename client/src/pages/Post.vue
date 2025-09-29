<template>
  <div v-if="post">
    <h1>{{ post.title }}</h1>
    <article class="article" v-html="post.body"></article>
  </div>

  <div v-else>
    <p>Loading post...</p>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'

const post = ref(null)
const route = useRoute()

onMounted(async () => {
  try {
    const res = await fetch(`http://localhost:5000/api/posts/${route.params.id}`, {
      credentials: 'include'
    })
    const data = await res.json()
    post.value = data.data
  } catch (err) {
    console.error('Failed to fetch post:', err)
  }
})
</script>
