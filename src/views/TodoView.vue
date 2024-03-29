<template>
  <div class="container" style="display: none">
    <input type="text" v-model="newTodo" @keyup.enter="addTodo" />
    <button type="button" @click="addTodo">新增資資料</button>

    <ul>
      <li v-for="todo in filterTodos" :key="todo.id">
        <div v-if="editTodoId === todo.id">
          <input type="text" v-model="editTodoText" @keyup.enter="completeEdit(todo)" />
        </div>
        <div v-else>
          <input type="checkbox" v-model="todo.done" />
          <span @dblclick="editTodo(todo)" :class="{ done: todo.done }">{{ todo.text }}</span>
          <button type="button" @click="removeTodo(todo.id)">X</button>
        </div>
      </li>
      <button type="button" :disabled="visibility === 'all'" @click="visibility = 'all'">
        全部
      </button>
      <button type="button" :disabled="visibility === 'active'" @click="visibility = 'active'">
        待完成
      </button>
      <button
        type="button"
        :disabled="visibility === 'completed'"
        @click="visibility = 'completed'"
      >
        已完成
      </button>
    </ul>
    <hr />
  </div>

  <div class="bg-half">
    <div class="conatiner todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input
            type="text"
            placeholder="請輸入待辦事項"
            v-model="newTodo"
            @keyup.enter="addTodo"
          />
          <a href="#" @click="addTodo">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="22"
              height="22"
              fill="currentColor"
              class="bi bi-plus-lg"
              viewBox="0 0 16 16"
            >
              <path
                fill-rule="evenodd"
                d="M8 2a.5.5 0 0 1 .5.5v5h5a.5.5 0 0 1 0 1h-5v5a.5.5 0 0 1-1 0v-5h-5a.5.5 0 0 1 0-1h5v-5A.5.5 0 0 1 8 2"
              />
            </svg>
          </a>
        </div>
        <div class="todoList_list">
          <ul class="todoList_tab">
            <li>
              <a
                href="#"
                :class="{ active: visibility === 'all' }"
                @click.prevent="visibility = 'all'"
                >全部</a
              >
            </li>
            <li>
              <a
                href="#"
                :class="{ active: visibility === 'active' }"
                @click.prevent="visibility = 'active'"
                >待完成</a
              >
            </li>
            <li>
              <a
                href="#"
                :class="{ active: visibility === 'completed' }"
                @click.prevent="visibility = 'completed'"
                >已完成</a
              >
            </li>
          </ul>
          <div class="todoList_items">
            <ul class="todoList_item">
              <li v-for="todo in filterTodos" :key="todo.id">
                <!-- 修改中 -->
                <!-- <div v-if="editTodoId === todo.id">
                  <input type="text" v-model="editTodoText" @keyup.enter="completeEdit(todo)" />
                </div> -->
                <label class="todoList_label" v-if="editTodoId === todo.id">
                  <input
                    v-model="editTodoText"
                    type="text"
                    @keyup.enter="completeEdit(todo)"
                    @keyup.esc="editingTodoId = null"
                  />
                </label>
                <label class="todoList_label" v-else>
                  <input class="todoList_input" type="checkbox" v-model="todo.done" />
                  <span @dblclick="editTodo(todo)">{{ todo.text }}</span>
                </label>
                <a href="#" @click.prevent="removeTodo(todo.id)">
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="24"
                    height="24"
                    fill="currentColor"
                    class="bi bi-x"
                    viewBox="0 0 16 16"
                  >
                    <path
                      d="M4.646 4.646a.5.5 0 0 1 .708 0L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 0 1 0-.708"
                    />
                  </svg>
                </a>
              </li>
            </ul>
            <div class="todoList_statistics">
              <p>{{ todos.filter((todo) => todo.done).length }} 個已完成項目</p>
              <a href="#" @click.prevent="clearCompleted">清除已完成項目</a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
// 新增資料
// 呈現資料
// 可以移除指定資料
// 可以切換指定資料的狀態（done）
// 可以編輯指定資料的內容
// 可以依據狀態，呈現資料的內容（使用 computed
// 每次輸入文字時，自動儲存至 LocalStorage

import { computed, ref, watchEffect } from 'vue'

// localStorage 中的表單數據，並解決資料為空的狀態
const savedTodoData = localStorage.getItem('yuan-data') || '[]'
const todosData = ref(JSON.parse(savedTodoData))

const newTodo = ref('')
const todos = ref(todosData)

const addTodo = () => {
  if (newTodo.value) {
    // 陣列原型方法 > push
    todos.value.push({
      id: Date.now(),
      text: newTodo.value,
      done: false
    })
  }
  newTodo.value = ''
}

const removeTodo = (id) => {
  // console.log(id);
  // const index = ... , splice
  // React
  // 直接定義被刪除後的結果
  // const removeTodoList = todos.value.filter((todo) => todo.id !== id)
  // console.log(removeTodoList)
  // todos.value = removeTodoList

  // 縮寫
  todos.value = todos.value.filter((todo) => todo.id !== id)
}

// 編輯中
const editTodoId = ref(null)
const editTodoText = ref('')
const editTodo = (todo) => {
  editTodoId.value = todo.id
  editTodoText.value = todo.text
}

const completeEdit = (todo) => {
  const index = todos.value.findIndex((item) => item.id === todo.id)
  todos.value[index].text = editTodoText.value

  editTodoId.value = ''
  editTodoText.value = ''
}

// 狀態
const visibility = ref('all')
const filterTodos = computed((todo) => {
  if (visibility.value === 'active') {
    return todos.value.filter((todo) => !todo.done)
  } else if (visibility.value === 'completed') {
    return todos.value.filter((todo) => todo.done)
  } else {
    return todos.value
  }
})

// 清除已完成的 Todos
const clearCompleted = () => {
  todos.value = todos.value.filter((todo) => !todo.done)
}

// 自動儲存
watchEffect(() => {
  localStorage.setItem('yuan-data', JSON.stringify(todos.value))
})
</script>

<style>
.done {
  text-decoration: line-through;
}
</style>
