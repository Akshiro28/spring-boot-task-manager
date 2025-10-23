<template>
  <div class="min-h-screen max-h-[1080px] h-full mt-32">
    <div class="sm:max-w-xl md:max-w-2xl lg:max-w-4xl xl:max-w-6xl w-[calc(100%-32px)] mx-auto">
      <h1 class="text-3xl font-bold mb-6 text-center">Your tasks:</h1>

      <form @submit.prevent="addTask" class="flex gap-2 mb-6 max-w-2/3 mx-auto">
        <input
          v-model="newTask"
          type="text"
          placeholder="Enter a new task..."
          class="flex-grow px-6 py-2 border border-[var(--bg-lighter)] rounded-full focus:outline-none focus:border-[var(--accent-light)]"
        />
        <button
          type="submit"
          class="bg-[var(--accent-light)] px-6 py-2 rounded-full hover:bg-[var(--accent-light)]/80 transition cursor-pointer font-semibold"
        >
          Add Task
        </button>
      </form>

      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
        <div
          v-for="task in tasks"
          :key="task.id"
          class="flex flex-col justify-between bg-[var(--bg-light)] p-6 rounded-2xl shadow-md border border-[var(--bg-lighter)] hover:shadow-lg transition"
        >
          <p v-if="task.createdAt" class="text-xs text-gray-400 mb-2">
            {{ formatDate(task.createdAt) }}
          </p>

          <div class="flex-1">
            <div class="flex items-center mb-2">
              <input
                type="checkbox"
                v-model="task.completed"
                @change="updateTask(task)"
                class="mr-2 accent-blue-600"
              />
              <h2
                class="text-lg font-semibold break-words"
                :class="{ 'line-through text-gray-500': task.completed }"
              >
                {{ task.title }}
              </h2>
            </div>

            <p v-if="task.description" class="text-gray-600 text-sm mt-1 break-words">
              {{ task.description }}
            </p>

            <div v-if="editingDescId === task.id" class="mt-3">
              <textarea
                v-model="tempDescription"
                type="text"
                placeholder="Enter description..."
                class="w-full px-4 py-2 border border-[var(--bg-lighter)] rounded-lg focus:outline-none focus:ring-2 focus:ring-blue-500"
              />
              <div class="flex gap-2 mt-2">
                <button
                  @click="saveTaskDescription(task)"
                  class="flex-grow bg-[var(--accent-light)] py-2 rounded-full hover:bg-[var(--accent-light)]/80 cursor-pointer transition"
                >
                  Save
                </button>
                <button
                  @click="cancelEditingDesc"
                  class="flex-grow bg-[var(--bg-lighter)] py-2 rounded-full hover:bg-[var(--bg-lighter)]/80 cursor-pointer transition"
                >
                  Cancel
                </button>
              </div>
            </div>
          </div>

          <div class="flex justify-between items-center mt-4">
            <button
              @click="startEditingDesc(task)"
              class="text-blue-500 hover:text-blue-700 font-semibold cursor-pointer text-sm"
            >
              Edit description
            </button>
            <button
              @click="deleteTask(task.id)"
              class="text-red-500 hover:text-red-700 font-semibold cursor-pointer text-sm"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'

type Task = {
  id: number
  title: string
  completed: boolean
  description?: string
  createdAt?: string
}

const tasks = ref<Task[]>([])
const newTask = ref('')
const editingDescId = ref<number | null>(null)
const tempDescription = ref('')

function formatDate(dateStr: string) {
  const date = new Date(dateStr)
  return date.toLocaleString(undefined, {
    year: 'numeric',
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit',
  })
}

async function fetchTasks() {
  const res = await fetch('/api/tasks')
  tasks.value = await res.json()
}

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

async function updateTask(task: Task) {
  await fetch(`/api/tasks/${task.id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(task),
  })
}

function startEditingDesc(task: Task) {
  editingDescId.value = task.id
  tempDescription.value = task.description || ''
}

function cancelEditingDesc() {
  editingDescId.value = null
  tempDescription.value = ''
}

async function saveTaskDescription(task: Task) {
  const updatedTask = { ...task, description: tempDescription.value }

  await fetch(`/api/tasks/${task.id}`, {
    method: 'PUT',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(updatedTask),
  })

  const idx = tasks.value.findIndex((t) => t.id === task.id)
  if (idx !== -1 && tasks.value[idx]) {
    tasks.value[idx].description = tempDescription.value
  }

  cancelEditingDesc()
}

async function deleteTask(id: number) {
  await fetch(`/api/tasks/${id}`, { method: 'DELETE' })
  tasks.value = tasks.value.filter((t) => t.id !== id)
}

onMounted(fetchTasks)
</script>
