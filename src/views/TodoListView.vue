<script setup>
import axios from 'axios'
import { onMounted, ref } from 'vue'

const isLogin = ref(false)
const token = ref('')
const root = ref('https://todolist-api.hexschool.io')
const todoLists = ref('')
const addContent = ref('')
const temp = ref({
  id: '',
  createTime: 0,
  content: '',
  status: false
})

onMounted(async () => {
  await checkToken()
  if (isLogin) {
    await getTodoList()
  }
})

async function checkToken() {
  try {
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
  } catch (error) {
    console.log('Token檢查失敗', error)
  }
}

//取得TodoList
async function getTodoList() {
  const url = `${root.value}/todos/`
  try {
    const response = await axios.get(url, {
      headers: {
        Authorization: token.value
      }
    })

    if (response.status == 200) {
      todoLists.value = response.data.data
    }
  } catch (error) {
    console.log('取得TodoList失敗', error)
  }
}

//新增TODO
async function addTodo() {
  const url = `${root.value}/todos/`
  try {
    const response = await axios.post(
      url,
      {
        content: addContent.value
      },
      {
        headers: {
          Authorization: token.value
        }
      }
    )
  } catch (error) {
    console.log('新增失敗', error)
  }
  await getTodoList()
}

//[編輯]按鈕事件
function editItem(id) {
  const index = todoLists.value.findIndex((item) => item.id == id)
  temp.value = { ...todoLists.value[index] }
}

//更新TODO
async function updateTodos() {
  const url = `${root.value}/todos/${temp.value.id}`
  try {
    const response = await axios.put(
      url,
      {
        content: temp.value.content
      },
      {
        headers: {
          Authorization: token.value
        }
      }
    )
  } catch (error) {
    console.log('更新失敗', error)
  }
  temp.value = {}
  await getTodoList()
}

//刪除TODO
async function deleteTodo(id) {
  const url = `${root.value}/todos/${id}`
  try {
    const response = await axios.delete(url, {
      headers: {
        Authorization: token.value
      }
    })
  } catch (error) {
    console.log('刪除失敗', error)
  }
  temp.value = {}
  await getTodoList()
}

//更新Toggle
async function updateToggle(id) {
  const url = `${root.value}/todos/${id}/toggle`
  try {
    const response = await axios.patch(
      url,
      {},
      {
        headers: {
          Authorization: token.value
        }
      }
    )
  } catch (error) {
    console.log('更新toggle失敗', error)
  }
  await getTodoList()
}
</script>

<template>
  <h6>token</h6>
  {{ token }}
  <hr />
  <h6>addContent</h6>
  {{ addContent }}
  <hr />
  <h6>temp:</h6>
  {{ temp }}
  <hr />
  <h6>todoLists:</h6>
  {{ todoLists }}
  <hr />
  <div class="container-fluid">
    <div class="row" v-if="!isLogin">
      <div class="col-12 text-center">
        <H1>尚未登入</H1>
        <button class="btn btn-dark" type="button">
          <RouterLink class="nav-link" to="/Login">返回登入頁面</RouterLink>
        </button>
      </div>
    </div>
    <div class="row">
      <div class="col-12">
        <table class="table table-success table-striped table-hover">
          <thead>
            <tr>
              <th scope="col"><p>ID</p></th>
              <th scope="col"><p>內容</p></th>
              <th scope="col"><p>動作</p></th>
              <th scope="col"><p>建立時間</p></th>
              <th scope="col"><p>是否完成</p></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in todoLists" key="{{item.id}}">
              <td class="">
                <p>{{ item.id }}</p>
              </td>
              <td class="w-25">
                <input type="text" v-if="temp.id === item.id" v-model="temp.content" />
                <p v-if="temp.id !== item.id">{{ item.content }}</p>
              </td>
              <td>
                <button
                  v-if="temp.id !== item.id"
                  class="btn btn-success d-inline-block me-md-1"
                  type="button"
                  @click.prevent="editItem(item.id)"
                >
                  編輯
                </button>
                <button
                  v-if="temp.id !== item.id"
                  class="btn btn-danger d-inline-block"
                  type="button"
                  @click.prevent="deleteTodo(item.id)"
                >
                  刪除
                </button>
                <button
                  v-if="temp.id === item.id"
                  class="btn btn-success d-inline-block me-md-1"
                  type="button"
                  @click.prevent="updateTodos()"
                >
                  確認
                </button>
                <button
                  v-if="temp.id === item.id"
                  class="btn btn-secondary d-inline-block"
                  type="button"
                  @click.prevent="temp = {}"
                >
                  取消
                </button>
              </td>
              <td>
                <p>{{ item.createTime }}</p>
              </td>
              <td>
                <input
                  type="checkbox"
                  name=""
                  :checked="item.status"
                  @click.prevent="updateToggle(item.id)"
                />
              </td>
            </tr>
            <tr>
              <td class="w-25">
                <input type="text" v-model="addContent" placeholder="請輸入要新增的內容" />
              </td>
              <td>
                <button
                  class="btn btn-success d-inline-block"
                  type="button"
                  @click.prevent="addTodo()"
                >
                  新增
                </button>
              </td>
              <td></td>
              <td></td>
              <td></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<style scoped>
td,
th {
  text-align: center;
  vertical-align: middle;
}

td p,
th p {
  margin: auto 0px;
}
</style>
