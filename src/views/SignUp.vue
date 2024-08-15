<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'

//註冊
const root = ref('https://todolist-api.hexschool.io')
const signUpInfomation = ref({
  email: 'floya9733@gmail.com',
  password: 'Abc123456',
  nickname: 'RRR'
})

const isSignUpSuccess = ref(false)
async function signUp() {
  const url = `${root.value}/users/sign_up`
  try {
    const response = await axios.post(url, signUpInfomation.value)
    alert('註冊成功!')
    isSignUpSuccess.value = true
  } catch (error) {
    alert(error.response.data.message)
  }
}
</script>

<template>
  <H1 class="text-center">註冊</H1>
  <div class="container-fluid">
    <div class="row" v-if="!isSignUpSuccess">
      <div class="col-12 d-flex align-item-strech justify-content-center">
        <div class="card text-dark bg-light mb-3 w-50">
          <div class="card-header">註冊</div>
          <div class="card-body d-flex align-item-strech justify-content-center flex-column">
            <h5 class="card-title">請填入您的資料!</h5>
            <p class="card-text">
              <input class="d-block w-100" type="text" v-model="signUpInfomation.email" />
              <input class="d-block w-100" type="text" v-model="signUpInfomation.password" />
              <input class="d-block w-100" type="text" v-model="signUpInfomation.nickname" />
            </p>
            <button class="btn btn-dark" type="button" @click="signUp()">註冊</button>
          </div>
        </div>
      </div>
    </div>
    <div class="row" v-if="isSignUpSuccess">
      <div class="col-12 d-flex align-item-strech justify-content-center">
        <div class="card text-dark bg-light mb-3 w-50">
          <div class="card-header">返回登入頁面</div>
          <div class="card-body d-flex align-item-strech justify-content-center flex-column">
            <button class="btn btn-dark" type="button">
              <RouterLink class="nav-link" to="/week">點此返回登入頁面</RouterLink>
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
