<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'

const isLogin = ref(false)
const uid = ref('')
const token = ref('')
const userName = ref('')
const root = ref('https://todolist-api.hexschool.io')
const signUpInfomation = ref({
  email: 'floya9733@gmail.com',
  password: 'Abc123456',
  nickname: 'RRR'
})
const userInfomation = ref({
  email: 'floya9733@gmail.com',
  password: 'Abc123456'
})

async function signUp() {
  const url = `${root.value}/users/sign_up`
  try {
    const response = await axios.post(url, signUpInfomation.value)
    uid.value = response.value.data.token
  } catch (error) {
    console.log(error.response.data.message)
  }
}

async function login() {
  const url = `${root.value}/users/sign_in`
  try {
    const response = await axios.post(url, signUpInfomation.value)
    console.log(response, response.value)
    token.value = response.data.token
    userName.value = response.data.nickname

    document.cookie = `hexToken=${token.value}`
  } catch (error) {
    console.log(error.response.data.message)
  }
}

async function signOut() {
  const url = `${root.value}/users/sign_out`
  try {
    const response = await axios.post(
      url,
      {},
      {
        headers: {
          Authorization: token.value
        }
      }
    )
    console.log(response, response.value)
    docCookies.removeItem(hexToken, root.value)
  } catch (error) {
    console.log(error.response.data.message)
  }
}

onMounted(async () => {
  token.value = document.cookie.replace(/(?:(?:^|.*;\s*)hexToken\s*\=\s*([^;]*).*$)|^.*$/, '$1')
  const url = `${root.value}/users/checkout`

  const response = await axios.get(url, {
    headers: {
      Authorization: token.value
    }
  })
  if (response.status == 200) {
    isLogin.value = true
  }
})
</script>

<template>
  <div class="signUp">
    <h1>註冊</h1>
    {{ signUpInfomation }}
    <hr />
    <input type="text" v-model="signUpInfomation.email" />
    <input type="text" v-model="signUpInfomation.password" />
    <input type="text" v-model="signUpInfomation.nickname" />
    <button type="button" @click="signUp()">註冊</button>
    {{ uid }}
  </div>
  <div class="login" v-if="!isLogin">
    <h1>登入</h1>
    {{ userInfomation }}
    <hr />
    <input type="text" v-model="userInfomation.email" />
    <input type="text" v-model="userInfomation.password" />
    <button type="button" @click="login()">登入</button>
    {{ token }}
  </div>
  <div class="check" v-if="isLogin">
    <h1>已登入</h1>
    {{ isLogin }}
    {{ token }}
  </div>
  <div class="signOut">
    <h1>登出</h1>
    <button v-if="isLogin" @click="signOut()">登出</button>
  </div>
</template>
