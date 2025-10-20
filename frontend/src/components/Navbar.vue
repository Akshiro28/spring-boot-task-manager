<script lang="ts" setup>
import { ref } from 'vue'
import logo from '@/assets/img/logo.png'

const isMenuOpen = ref(false)

const toggleMenu = () => {
  isMenuOpen.value = !isMenuOpen.value
}
</script>

<template>
  <nav
    class="bg-[var(--bg-light)] border border-[var(--bg-lighter)] shadow-md rounded-[33px] sm:max-w-xl md:max-w-2xl lg:max-w-4xl xl:max-w-6xl px-6 sm:px-8 fixed top-4 w-[calc(100%-32px)] left-1/2 -translate-x-1/2"
  >
    <div class="flex justify-between items-center h-16">
      <div class="flex-shrink-0">
        <a href="index.html" class="flex flex-shrink-0 items-center mr-4">
          <img :src="logo" alt="Logo" class="h-8 w-auto object-contain" />
        </a>
      </div>

      <div class="hidden sm:flex space-x-8">
        <RouterLink to="/" class="relative text-[var(--text)] transition after:content-[''] after:absolute after:left-0 after:bottom-0 after:h-[1px] after:w-0 after:bg-[var(--text-light)] after:transition-all after:duration-300 hover:after:w-full">Home</RouterLink>
        <RouterLink to="/tasks" class="relative text-[var(--text)] transition after:content-[''] after:absolute after:left-0 after:bottom-0 after:h-[1px] after:w-0 after:bg-[var(--text-light)] after:transition-all after:duration-300 hover:after:w-full">Tasks</RouterLink>
      </div>

      <!-- Mobile -->
      <div class="sm:hidden h-6">
        <button
          @click="toggleMenu"
          class="text-[var(--text)] hover:text-[var(--text-light)] focus:outline-none"
        >
          <svg
            v-if="!isMenuOpen"
            class="h-6 w-6"
            fill="none"
            stroke="currentColor"
            viewBox="0 0 24 24"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4 6h16M4 12h16M4 18h16"
            />
          </svg>
          <svg v-else class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M6 18L18 6M6 6l12 12"
            />
          </svg>
        </button>
      </div>
    </div>

    <!-- Mobile -->
    <div v-show="isMenuOpen" class="sm:hidden pt-2 pb-3 space-y-1 transition-all duration-300">
      <div class="flex flex-col space-y-2">
        <RouterLink to="/" class="relative py-1 w-fit text-[var(--text)] transition after:content-[''] after:absolute after:left-0 after:bottom-0 after:h-[1px] after:w-0 after:bg-[var(--text-light)] after:transition-all after:duration-300 hover:after:w-full">Home</RouterLink>
        <RouterLink to="/tasks" class="relative py-1 w-fit text-[var(--text)] transition after:content-[''] after:absolute after:left-0 after:bottom-0 after:h-[1px] after:w-0 after:bg-[var(--text-light)] after:transition-all after:duration-300 hover:after:w-full">Tasks</RouterLink>
      </div>
    </div>
  </nav>
</template>
