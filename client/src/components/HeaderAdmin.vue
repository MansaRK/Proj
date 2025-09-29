<template>
  <header class="header">
    <RouterLink to="/" class="header__logo">UmAdmin</RouterLink>

    <nav class="header__nav">
      <ul>
        <li><RouterLink to="/">Home</RouterLink></li>
        <li><RouterLink to="/about">About</RouterLink></li>
        <li><RouterLink to="/contact">Contact</RouterLink></li>
      </ul>
    </nav>

    <div class="header__button">
      <!-- Show Login if NOT logged in -->
      <RouterLink v-if="!isLoggedIn" to="/admin/login" class="login-btn">Login</RouterLink>

      <!-- Show Logout if logged in -->
      <button v-else @click="logout" class="logout-btn">Logout</button>
    </div>
  </header>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const isLoggedIn = ref(false) // Reactive state to track login status

// Check login status when the component is mounted
onMounted(async () => {
  await checkAuthStatus()
})

// Watch for route changes and update login status accordingly
watch(() => router.currentRoute.value.path, async (newPath) => {
  if (newPath.includes('dashboard')) {
    await checkAuthStatus() // Recheck auth status if the route is dashboard
  }
})

// Function to check login status
async function checkAuthStatus() {
  try {
    const res = await fetch('/api/admin/check-auth', {
      method: 'GET',
      credentials: 'include', // Include cookies (JWT token)
    })

    const json = await res.json()
    isLoggedIn.value = json.loggedIn // Update the reactive state based on the response
  } catch (err) {
    console.error('Auth check failed:', err)
    isLoggedIn.value = false // User is not logged in
  }
}

// Function to log out
async function logout() {
  try {
    const res = await fetch('/api/admin/logout', {
      method: 'GET',
      credentials: 'include', // Include cookies (JWT token)
    })

    if (res.ok) {
      isLoggedIn.value = false // Immediately set the login state to false
      router.push('/') // Redirect to home page or login page
    } else {
      console.error('Logout failed')
    }
  } catch (err) {
    console.error('Error logging out:', err)
  }
}
</script>

<style scoped>
/* Styling for Login and Logout buttons */
.login-btn,
.logout-btn {
  background-color: transparent;
  border: none;
  color: #f44336;
  cursor: pointer;
  font-weight: bold;
  font-size: 1em;
}

.login-btn:hover,
.logout-btn:hover {
  text-decoration: underline;
}
</style>
