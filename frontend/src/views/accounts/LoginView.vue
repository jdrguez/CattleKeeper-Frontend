<script setup lang="ts">
import { ref, inject } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'
import { RouterLink } from 'vue-router'

const username = ref('')
const password = ref('')
const router = useRouter()
const backendUrl = 'https://cattlekeeper-backend-production.up.railway.app'
const auth = inject<{ isLoggedIn: boolean }>('auth')!
import { useToast } from 'vue-toastification';

const toast = useToast()
const login = async () => {
  try {
    const response = await axios.post(`${backendUrl}/api/accounts/login/`, {
      username: username.value,
      password: password.value,
    })

    const token = response.headers['authorization']
    localStorage.setItem('token', token)
    auth.isLoggedIn = true

    await router.push('/dashboard')
  } catch (err) {
    toast.error('Invalid credentials')
    console.error(err)
  }
}
</script>

<template>
  <div class="d-flex align-items-center justify-content-center vh-50 mb-4">
    <div
      class="card shadow-lg p-4"
      style="width: 100%; max-width: 400px; border-radius: 20px; background-color: #015730; color: white; position: relative"
    >
      <h3 class="text-center mb-4">Login</h3>

      <form @submit.prevent="login">
        <div class="mb-3">
          <label for="username" class="form-label">Username</label>
          <input
            v-model="username"
            type="text"
            class="form-control rounded-pill"
            id="username"
            placeholder="Enter your username"
          />
        </div>

        <div class="mb-4">
          <label for="password" class="form-label">Password</label>
          <input
            v-model="password"
            type="password"
            class="form-control rounded-pill"
            id="password"
            placeholder="Password"
          />
        </div>

        <button type="submit" class="btn w-100 rounded-pill text-white" style="background-color: #f38b2c">
          Login
        </button>

        <div class="text-center mt-3">
          <RouterLink to="/signup" class="text-decoration-none text-warning">Don't have an account? Sign Up</RouterLink>
        </div>
      </form>    
    </div>
  </div>
</template>
