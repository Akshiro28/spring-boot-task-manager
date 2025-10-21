<template>
  <div class="min-h-screen max-h-[1080px] h-full mt-32">
    <div class="max-w-2xl mx-auto bg-[var(--bg-light)] border border-[var(--bg-lighter)] p-6 rounded-2xl shadow-lg">
      <h1 class="text-3xl font-bold mb-6 text-center text-blue-600">Task Manager</h1>

      <!-- Add Task Form -->
      <form @submit.prevent="addTask" class="flex gap-2 mb-6">
        <input
          v-model="newTask"
          type="text"
          placeholder="Enter a new task..."
          class="flex-grow p-2 border border-[var(--bg-lighter)] rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
        />
        <button
          type="submit"
          class="bg-blue-600 text-white px-4 py-2 rounded-lg hover:bg-blue-700 transition"
        >
          Add
        </button>
      </form>

      <!-- Task List -->
      <ul>
        <li
          v-for="task in tasks"
          :key="task.id"
          class="flex justify-between items-center p-3 border-b border-gray-200"
        >
          <div>
            <input
              type="checkbox"
              v-model="task.completed"
              @change="updateTask(task)"
              class="mr-2 accent-blue-600"
            />
            <span :class="{ 'line-through text-gray-500': task.completed }">
              {{ task.title }}
            </span>
          </div>
          <button
            @click="deleteTask(task.id)"
            class="text-red-500 hover:text-red-700"
          >
            Delete
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

type Task = {
  id: number
  title: string
  completed: boolean
}

const tasks = ref<Task[]>([])
const newTask = ref('')

// Fetch all tasks
async function fetchTasks() {
  const res = await fetch('/api/tasks')
  tasks.value = await res.json()
}

// Add new task
async function addTask() {
  if (!newTask.value.trim()) return
  const res = await fetch('/api/tasks', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ title: newTask.value, completed: false }),
  })
  const created = await res.json()
  tasks.value.push(created)
  newTask.value = ''
}

// Update task
async function updateTask(task: Task) {
  await fetch(`/api/tasks/${task.id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(task),
  })
}

// Delete task
async function deleteTask(id: number) {
  await fetch(`/api/tasks/${id}`, { method: 'DELETE' })
  tasks.value = tasks.value.filter((t) => t.id !== id)
}

onMounted(fetchTasks)
</script>
