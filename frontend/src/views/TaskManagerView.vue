<template>
  <div class="min-h-screen max-h-[1080px] h-full mt-32">
    <div class="max-w-2xl mx-auto">
      <h1 class="text-3xl font-bold mb-6 text-center">Your tasks:</h1>

      <form @submit.prevent="addTask" class="flex gap-2 mb-6">
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

      <ul>
        <li
          v-for="task in tasks"
          :key="task.id"
          class="mb-4 px-6 py-3 bg-[var(--bg-light)] rounded-2xl shadow-sm"
        >
          <div class="flex justify-between items-center">
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
            <div class="flex gap-4">
              <button
                @click="startEditingDesc(task)"
                class="text-blue-500 hover:text-blue-700 font-semibold cursor-pointer"
              >
                Edit description
              </button>
              <button
                @click="deleteTask(task.id)"
                class="text-red-500 hover:text-red-700 font-semibold cursor-pointer"
              >
                Delete
              </button>
            </div>
          </div>

          <!-- Description box -->
          <div v-if="editingDescId === task.id" class="mt-3 flex gap-2">
            <input
              v-model="tempDescription"
              type="text"
              placeholder="Enter description..."
              class="flex-grow px-4 py-2 border border-[var(--bg-lighter)] rounded-full focus:outline-none focus:ring-2 focus:ring-blue-500"
            />
            <button
              @click="saveTaskDescription(task)"
              class="bg-[var(--accent-light)] px-4 py-2 rounded-full hover:bg-[var(--accent-light)]/80 cursor-pointer transition"
            >
              Save
            </button>
            <button
              @click="cancelEditingDesc"
              class="bg-[var(--bg-lighter)] px-4 py-2 rounded-full hover:bg-[var(--bg-lighter)]/80 cursor-pointer transition"
            >
              Cancel
            </button>
          </div>

          <p v-else-if="task.description" class="mt-2 text-gray-600">
            {{ task.description }}
          </p>
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
  description?: string
}

const tasks = ref<Task[]>([])
const newTask = ref('')
const editingDescId = ref<number | null>(null)
const tempDescription = ref('')

// fetchTasks
async function fetchTasks() {
  const res = await fetch('/api/tasks')
  tasks.value = await res.json()
}

// addTask
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

// updateTask
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

// deleteTask
async function deleteTask(id: number) {
  await fetch(`/api/tasks/${id}`, { method: 'DELETE' })
  tasks.value = tasks.value.filter((t) => t.id !== id)
}

onMounted(fetchTasks)
</script>
