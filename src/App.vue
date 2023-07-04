<script setup>
import {  ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if(input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
}

const removetoDo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal) 
})

onMounted (() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>

<main class="app">
  <section class="greeting">
    <h2 class="pb-4 title text-secondary">
      Hello there, <input type="text" placeholder="Name here" class="text-white" v-model="name">
    </h2>
  </section>

  <section class="create-todo">
    <form @submit.prevent="addTodo">
      <input type="text" class="h4 text-white border-secondary" placeholder="What needs done?" v-model="input_content">

      <h4 class="pt-5 text-secondary">Pick a category</h4>

      <div class="options pt-3 pb-3 text-white">

        <label>
          <input type="radio" name="category" id="category1" value="business" v-model="input_category">
          <span class="bubble business"></span>
          <div>Business</div>
        </label>

        <label>
          <input type="radio" name="category" id="category2" value="personal" v-model="input_category">
          <span class="bubble personal"></span>
          <div>Personal</div>
        </label>

        <label>
          <input type="radio" name="category" id="category3" value="school" v-model="input_category">
          <span class="bubble school"></span>
          <div>School</div>
        </label>

      </div>

      <button type="submit" class="btn btn-success fw-bold" value="Add todo">ADD</button>

    </form>
  </section>

  <section class="todo-list">
    <h3>Todo List</h3>
    <div class="list">
      <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
        <label>
          <input type="checkbox" v-model="todo.done">
          <span :class="`bubble ${todo.category}`"></span>
        </label>

        <div class="todo-content">
          <input type="text" v-model="todo.content">
        </div>

        <div class="actions">
          <button type="button" class="btn btn-danger fw-bold" @click="removetoDo(todo)">DELETE</button>
        </div>
      </div>
    </div>
  </section>
</main>

</template>


