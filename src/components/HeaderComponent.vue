<script setup lang="ts">
import { RouterLink } from 'vue-router'
import { store } from '@/store'
import { API_URL } from '@/environment'
import { getCookie } from '@/helpers'

const session = getCookie('refreshToken')
if (session) {
  fetch(`${API_URL}/api/user/self`, {
    credentials: 'include',
  })
    .then((res) => {
      if (res.ok) {
        return res.json()
      }
    })
    .then((data) => {
      store.user = data
    })
}

const logout = () => {
  const csrfToken = getCookie('csrftoken')
  if (csrfToken) {
    fetch(`${API_URL}/api/user/auth/logout`, {
      method: 'POST',
      headers: {
        'X-CSRFToken': csrfToken,
      },
      credentials: 'include',
    })
  }

  document.cookie = 'refreshToken=; expires=Thu, 01 Jan 1970 00:00:00 UTC; path=/;'
  store.user = null
}
</script>

<template>
  <div class="header-container">
    <nav>
      <RouterLink to="/">Home</RouterLink>
      <RouterLink to="/">About</RouterLink>
    </nav>
    <div class="user-info" v-if="store.user">
      <RouterLink to="/profile" class="header-username">{{ store.user.username }}</RouterLink> |
      <RouterLink to="?" class="logout" @click="logout">Logout</RouterLink>
    </div>
    <div v-else>
      <RouterLink to="/login">Login</RouterLink> |
      <RouterLink to="/Signup">Signup</RouterLink>
    </div>
  </div>
</template>

<style scoped>
.header-container {
  display: flex;

  justify-content: space-between;
  align-items: center;

  padding: var(--spacing-4);
}

.header-username {
  padding-right: var(--spacing-8);
}
</style>
