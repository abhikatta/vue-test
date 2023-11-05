<script setup>
import { ref, computed, watch, onMounted } from 'vue'
const todos = ref([])
const inputContext = ref('')
const sortTodos = computed(() =>
  // used slice here because vue says original array (todos ref) should not be modified directly
  // instead this slice will create a copy of the whole array and works on it,
  // don't know why it works but it works

  todos.value.slice().sort((a, b) => {
    return b.timestamp - a.timestamp
  })
)
const formattedTimestamp = (timestamp) => {
  const date = new Date(timestamp)
  return ` ${date.toLocaleDateString()} ${date.toLocaleTimeString()}`
}
const addTodo = () => {
  if (inputContext.value.trim() === '' || inputContext.value === null) {
    return
  } else {
    return todos.value.push({
      content: inputContext.value,
      checked: false,
      timestamp: new Date().getTime()
    })
  }
}
const deleteTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}
watch(
  todos,
  (newTodo) => {
    localStorage.setItem('todos', JSON.stringify(newTodo))
    inputContext.value = ''
  },
  { deep: true }
)

onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>
<template>
  <main
    class="w-full min-w-screen h-full min-h-screen flex flex-col items-center bg-gray-600 py-10"
  >
    <div class="flex flex-col items-center">
      <h3 class="text-gray-100 py-10 text-3xl">Write a todo</h3>
      <form @submit.prevent="addTodo" class="flex flex-col items-center my-10">
        <input
          class="text-3xl text-center px-2 py-1 rounded-md outline-none"
          placeholder="e.g workout at 5 today"
          type="text"
          v-model="inputContext"
        />
        <button
          type="submit"
          class="w-[60%] py-1 my-2 bg-gray-500 rounded-md font-thin text-gray-200"
          :on-click="addTodo"
        >
          Add todo
        </button>
      </form>
    </div>
    <!-- display todos: -->
    <div class="rounded-3xl border-gray-400 border-[3px]">
      <div v-if="todos.length === 0" class="px-10 py-3">
        <p class="text-left text-xl px-2 py-1 rounded-md outline-none text-gray-300 font-bold">
          Seems like you got nothing to do...
        </p>
      </div>
      <div v-for="todo in sortTodos" :key="todo">
        <div class="flex flex-col">
          <div class="flex flex-row px-4 py-3 items-center">
            <div class="h-9 w-9 flex flex-col px-2 bg-clip-border items-center justify-center">
              <input type="checkbox" class="h-full w-full" v-model="todo.checked" />
            </div>
            <input
              type="text"
              :title="`Added at:${formattedTimestamp(todo.timestamp)}`"
              class="text-left text-xl px-2 py-1 bg-gray-700 rounded-md outline-none text-gray-400 font-bold"
              v-model="todo.content"
            />
            <button
              @click="deleteTodo(todo)"
              class="bg-red-400 rounded-lg h-[2.2rem] px-2 py-1 ml-2 text-gray-900 font-bold"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>
