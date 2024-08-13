<script setup>
import axios from 'axios'

login()
async function login() {
  try {
    const response = await axios.get('https://randomuser.me/ap')
    console.log(response, response.value)
  } catch (error) {
    console.log(error, error.response)
  }
}
</script>

<template>
  <h1>Hello</h1>
</template>
