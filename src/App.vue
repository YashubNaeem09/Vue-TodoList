<script setup>
import {
  ref,
  onMounted,
  computed,
  watch
} from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const sortTodos = () => {
  return todos.value.slice().sort((a, b) => {
    return b.createdAt - a.createdAt;
  });
};

const todos_asc = computed(() => sortTodos());

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
}
const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's Up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your Todo-List?</h4>
        <input type="text" placeholder="e.g: Wash my Car" v-model="input_content">
        <h1>Pick a Category:</h1>
        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add Todo">
      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :key="todo.id" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category == 'business'? 'business':'personal'}`">
            </span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>
          <div class="actions">
          <button class="delete" v-on:click="removeTodo(todo)">Delete</button>
        </div>
        </div>
      </div>
    </section>
  </main>
</template>
