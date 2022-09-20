<template>
  <form @submit.prevent="handleLogin">
    <div class="form-group">
      <label>Username</label>
      <input type="text" name="username" required v-model="username" />
    </div>
    <div class="form-group">
      <label>Password</label>
      <input type="password" name="password" required v-model="password" />
    </div>
    <div class="form-button">
      <button class="button button-primary">Submit</button>
    </div>
  </form>
</template>

<script setup>
import { ref } from 'vue';
import { microgen } from '~~/lib/microgen.js';

const router = useRouter();
const username = ref('');
const password = ref('');

const handleLogin = async () => {
  const { error, token } = await microgen.auth.login({
    username: username.value,
    password: password.value,
  });

  if (error) {
    alert(error.message);
    return;
  }

  if (token) {
    router.push('/profile');
  }
};
</script>
