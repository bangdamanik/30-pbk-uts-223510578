<template>
  <main class="app">
    <section class="create-todo">
      <form @submit.prevent="addTodo">
        <input type="text" placeholder="Nama Kegiatan" v-model="input_content" />

        <h4>Tambahkan Kegiatan</h4>

        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Pribadi</div>
          </label><br>
        </div>

        <input type="submit" value="Tambahkan" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Kegiatan</h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" v-if="!todo.done" />
            <div class="done-text" v-else>{{ todo.content }}</div>
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Hapus</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])

const name = ref('')

const input_content = ref('')

const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

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

  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<style scoped>
.done .todo-content input {
  text-decoration: line-through;
}

.done .done-text {
  text-decoration: line-through;
}
</style>