<script setup lang="ts">
import { ref } from 'vue'
import GifGallery from './components/GifGallery.vue'

const sidebarOpen = ref(true)

const menuItems = [
  { icon: 'gif', label: '常用 GIF 圖庫', active: true },
]
</script>

<template>
  <div class="flex h-screen bg-[#0d1117] text-gray-100 overflow-hidden">
    <!-- Sidebar -->
    <aside
      class="flex flex-col bg-[#161b22] border-r border-gray-800 transition-all duration-300 ease-in-out flex-shrink-0"
      :class="sidebarOpen ? 'w-60' : 'w-16'"
    >
      <!-- Header / Toggle -->
      <div
        class="flex items-center h-16 border-b border-gray-800 px-4"
        :class="sidebarOpen ? 'justify-between' : 'justify-center'"
      >
        <span
          v-if="sidebarOpen"
          class="text-amber-400 font-semibold text-sm tracking-wide truncate"
        >後台管理</span>
        <button
          @click="sidebarOpen = !sidebarOpen"
          class="text-gray-500 hover:text-amber-400 transition-colors p-1.5 rounded-lg hover:bg-gray-800 cursor-pointer flex-shrink-0"
          :title="sidebarOpen ? '收起選單' : '展開選單'"
        >
          <!-- Collapse icon -->
          <svg
            v-if="sidebarOpen"
            xmlns="http://www.w3.org/2000/svg"
            class="w-5 h-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M11 19l-7-7 7-7m8 14l-7-7 7-7"
            />
          </svg>
          <!-- Expand icon -->
          <svg
            v-else
            xmlns="http://www.w3.org/2000/svg"
            class="w-5 h-5"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M13 5l7 7-7 7M5 5l7 7-7 7"
            />
          </svg>
        </button>
      </div>

      <!-- Navigation -->
      <nav class="flex-1 py-4 space-y-1">
        <a
          v-for="item in menuItems"
          :key="item.label"
          href="#"
          class="flex items-center gap-3 py-2.5 text-sm font-medium transition-all duration-150 rounded-xl mx-2 cursor-pointer"
          :class="[
            sidebarOpen ? 'px-3' : 'px-0 justify-center',
            item.active
              ? 'bg-amber-500/10 text-amber-400 border border-amber-500/20'
              : 'text-gray-400 hover:text-gray-100 hover:bg-gray-800 border border-transparent',
          ]"
          :title="!sidebarOpen ? item.label : undefined"
        >
          <!-- GIF Icon -->
          <svg
            v-if="item.icon === 'gif'"
            xmlns="http://www.w3.org/2000/svg"
            class="w-5 h-5 flex-shrink-0"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="2"
              d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"
            />
          </svg>
          <span v-if="sidebarOpen" class="truncate">{{ item.label }}</span>
        </a>
      </nav>
    </aside>

    <!-- Main Content -->
    <main class="flex-1 overflow-auto">
      <GifGallery />
    </main>
  </div>
</template>
