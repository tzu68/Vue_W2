<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'

const isLogin = ref(false)
const token = ref('')
const root = ref('https://todolist-api.hexschool.io')

const userInfomation = ref({
  email: 'floya9733@gmail.com',
  password: 'Abc123456'
})

//登入
async function login() {
  const url = `${root.value}/users/sign_in`
  try {
    const response = await axios.post(url, userInfomation.value)
    console.log(response)
    token.value = response.data.token
    document.cookie = `hexToken=${token.value}`
    alert('登入成功')
  } catch (error) {
    alert(error)
  }
}

//登出
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
    alert('登出成功')
  } catch (error) {
    alert(error)
  }
}

onMounted(async () => {
  //取得token
  token.value = document.cookie.replace(/(?:(?:^|.*;\s*)hexToken\s*\=\s*([^;]*).*$)|^.*$/, '$1')
  //進行token的驗證
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
  <div class="container-fluid">
    <div class="row" v-if="!isLogin">
      <div class="col-12 d-flex align-items-strech justify-content-center w-100">
        <div class="card text-dark bg-light mb-3 w-25">
          <div class="card-header">登入</div>
          <div class="card-body d-flex align-item-strech justify-content-center flex-column">
            <h5 class="card-title d-flex align-item-strech justify-content-center flex-column">
              請填入帳號密碼
            </h5>
            <p
              class="card-text d-flex align-item-strech justify-content-start flex-column"
              style="gap: 1vh"
            >
              <input type="text" v-model="userInfomation.email" />
              <input type="text" v-model="userInfomation.password" />
            </p>
            <button class="btn btn-dark" type="button" @click="login()">登入</button>
          </div>
        </div>
      </div>
    </div>
    <div class="row" v-if="isLogin">
      <div class="col-12 d-flex align-items-strech justify-content-center w-100">
        <div class="card text-dark bg-light mb-3 w-75">
          <div class="card-header">驗證Token</div>
          <div class="card-body d-flex align-items-center justify-content-center flex-column">
            <h5 class="card-title">已登入</h5>
            <p class="card-text w-75">{{ token }}</p>
            <button class="btn btn-dark w-25">
              <RouterLink class="nav-link" to="/TodoList">前往TodoList</RouterLink>
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="row">
      <div class="col-12 d-flex align-items-strech justify-content-center w-100">
        <div class="w-75 text-center">
          <button class="btn btn-dark w-25" v-if="isLogin" @click="signOut()">登出</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style></style>
