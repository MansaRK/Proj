<template>
  <div>
    <!-- Author intro -->
    <div class="author">
      <h1 class="author__heading">Yo, I am Oguri</h1>
      <p class="author__body">Ambitious Fatty Racist</p>
    </div>

    <img
      src="https://preview.redd.it/lrabshs4cpve1.png?width=640&crop=smart&auto=webp&s=2a6ea0651fb919f0528ac73f79adf28787ec5a81"
      alt="oguri"
      class="hero-image"
      width="981"
      height="528"
    />

    <!-- Articles section -->
    <section class="articles">
      <h2 class="articles__heading">Latest Posts</h2>

      <ul class="article-ul">
        <li v-for="post in posts" :key="post._id">
          <RouterLink :to="`/post/${post._id}`">
            <span>{{ post.title }}</span>
            <span class="article-list__date">
              {{ new Date(post.createdAt).toDateString() }}
            </span>
          </RouterLink>
        </li>
      </ul>

      <!-- Pagination -->
      <div class="pagination-wrapper">
        <div class="pagination-slot">
          <RouterLink
            v-if="nextPage"
            :to="`/?page=${nextPage}`"
            class="pagination"
          >
            &lt; Older Posts 
          </RouterLink>
        </div>
        <div class="pagination-slot"></div>
        <div class="pagination-slot">
          <RouterLink
            v-if="prevPage"
            :to="`/?page=${prevPage}`"
            class="pagination"
          >
            Newer Posts &gt;
          </RouterLink>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import { useRoute } from 'vue-router'

const posts = ref([])
const nextPage = ref(null)
const route = useRoute()
const prevPage = ref(null)
const API_URL = import.meta.env.VITE_API_URL

async function fetchPosts(page) {
  try {
    const res = await fetch(`${API_URL}/api/posts?page=${page}`, {
      credentials: 'include'
    })
    const data = await res.json()
    console.log('Fetched posts:', data)

    posts.value = data.data
    nextPage.value = data.nextPage
    prevPage.value = data.prevPage
  } catch (err) {
    console.error('Failed to fetch posts:', err)
  }
}

onMounted(() => {
  fetchPosts(route.query.page || 1)
})


watch(
  () => route.query.page,
  (newPage) => {
    fetchPosts(newPage || 1)
  }
)
</script>



