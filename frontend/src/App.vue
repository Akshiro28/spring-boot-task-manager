<script setup lang="ts">
import { RouterView } from 'vue-router'
import Navbar from '@/components/Navbar.vue'
import './assets/main.css'
</script>

<template>
  <div>
    <Navbar />

    <main class="p-4">
      <RouterView />
    </main>
  </div>
</template>
