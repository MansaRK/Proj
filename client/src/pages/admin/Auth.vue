<template>
  <div class="auth">
    <!-- Sign In -->
    <h3>Sign In</h3>
    <form @submit.prevent="login">
      <label for="login-username"><b>Username</b></label>
      <input
        type="text"
        id="login-username"
        v-model="loginUsername"
        placeholder="Enter Username"
        required
      />

      <label for="login-password"><b>Password</b></label>
      <input
        type="password"
        id="login-password"
        v-model="loginPassword"
        placeholder="Enter Password"
        required
      />

      <input type="submit" value="Login" class="btn" />
    </form>

    <!-- Register -->
    <h3>Register</h3>
    <form @submit.prevent="register">
      <label for="register-username"><b>Username</b></label>
      <input
        type="text"
        id="register-username"
        v-model="registerUsername"
        placeholder="Enter Username"
        required
      />

      <label for="register-password"><b>Password</b></label>
      <input
        type="password"
        id="register-password"
        v-model="registerPassword"
        placeholder="Enter Password"
        required
      />

      <input type="submit" value="Register" class="btn" />
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const loginUsername = ref('')
const loginPassword = ref('')
const registerUsername = ref('')
const registerPassword = ref('')

const router = useRouter()

// Login function
async function login() {
  try {
    const res = await fetch('/api/admin/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        username: loginUsername.value,
        password: loginPassword.value,
      }),
      credentials: 'include', // keep session cookies
    })

    if (res.ok) {
      router.push('/admin/dashboard')
    } else {
      alert('Login failed')
    }
  } catch (err) {
    console.error('Error logging in:', err)
  }
}

// Register function
async function register() {
  try {
    const res = await fetch('/api/admin/register', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        username: registerUsername.value,
        password: registerPassword.value,
      }),
    })

    if (res.ok) {
      alert('Registration successful, you can now log in')
      registerUsername.value = ''
      registerPassword.value = ''
    } else {
      alert('Registration failed')
    }
  } catch (err) {
    console.error('Error registering:', err)
  }
}
</script>

<style scoped>
.auth {
  max-width: 400px;
  margin: auto;
}
form {
  margin-bottom: 2rem;
}
</style>
