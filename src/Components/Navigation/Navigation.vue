<script setup lang="ts">
import { ref } from 'vue';

const username = ref("")
const password = ref("")

function submitLogin() {
    console.log(username.value, password.value);
}
</script>

<template>
    <form @submit.prevent="submitLogin">
        <label for="username">Username:</label>
        <input type="text" id="username" v-model="username" placeholder="username" />
        <label for="password">Password:</label>
        <input type="text" id="password" v-model="password" placeholder="password" />
        <input type="submit" value="Log in" />
    </form>
</template>