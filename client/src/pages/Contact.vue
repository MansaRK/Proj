<template>
  <div class="contact">
    <h1>Contact</h1>
    <p>Send us a message and we’ll get back to you.</p>

    <form @submit.prevent="submitForm">
      <label for="name"><b>Name</b></label>
      <input
        type="text"
        id="name"
        v-model="name"
        placeholder="Enter your name"
        required
      />

      <label for="email"><b>Email</b></label>
      <input
        type="email"
        id="email"
        v-model="email"
        placeholder="Enter your email"
        required
      />

      <label for="message"><b>Message</b></label>
      <textarea
        id="message"
        v-model="message"
        cols="50"
        rows="5"
        placeholder="Write your message"
        required
      ></textarea>

      <input type="submit" value="Send" class="btn" />
    </form>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const name = ref('')
const email = ref('')
const message = ref('')

async function submitForm() {
  try {
    const res = await fetch('/api/contact', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        name: name.value,
        email: email.value,
        message: message.value,
      }),
    })

    if (res.ok) {
      alert('Message sent successfully!')
      name.value = ''
      email.value = ''
      message.value = ''
    } else {
      alert('Failed to send message')
    }
  } catch (err) {
    console.error('Error submitting form:', err)
  }
}
</script>

<style scoped>
.contact {
  max-width: 600px;
  margin: auto;
  padding: 2rem;
}
form {
  display: flex;
  flex-direction: column;
}
label {
  margin-top: 1rem;
}
input,
textarea {
  padding: 0.5rem;
  margin-top: 0.5rem;
}
.btn {
  margin-top: 1rem;
  padding: 0.75rem;
  cursor: pointer;
}
</style>
