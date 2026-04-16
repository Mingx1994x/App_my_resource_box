<script setup lang="ts">
import { ref } from 'vue'
import CategoryModal from './CategoryModal.vue'

interface Category { label: string; color: string; description: string }

const categories = ref<Category[]>([
  { label: '所有', color: '#F59E0B', description: '' },
  { label: '可愛', color: '#EC4899', description: '' },
  { label: '常用', color: '#0EA5E9', description: '' },
])
const activeCategory = ref('所有')

const gifs = [
  {
    id: 1,
    title: '超萌貓咪跳舞',
    category: '可愛',
    url: 'https://media.giphy.com/media/JIX9t2j0ZTN9S/giphy.gif',
  },
  {
    id: 2,
    title: '比讚表情包',
    category: '常用',
    url: 'https://media.giphy.com/media/111ebonMs90YLu/giphy.gif',
  },
  {
    id: 3,
    title: '小狗搖尾巴',
    category: '可愛',
    url: 'https://media.giphy.com/media/mCRJDo24UvJMA/giphy.gif',
  },
]

const copied = ref<number | null>(null)

function copyUrl(id: number, url: string) {
  navigator.clipboard.writeText(url)
  copied.value = id
  setTimeout(() => {
    copied.value = null
  }, 2000)
}

const filteredGifs = () =>
  activeCategory.value === '所有'
    ? gifs
    : gifs.filter((g) => g.category === activeCategory.value)

// 以 hex 色碼產生半透明的類別色彩 style（篩選按鈕 active 與卡片標籤共用）
function categoryStyle(hex: string): Record<string, string> {
  return {
    color: hex,
    backgroundColor: hex + '26',  // ~15% opacity
    borderColor: hex + '40',       // ~25% opacity
  }
}

function colorForCategory(label: string): string {
  return categories.value.find(c => c.label === label)?.color ?? '#F59E0B'
}

const showCategoryModal = ref(false)
function handleCategoryConfirm(form: { name: string; color: string; description: string }) {
  categories.value.push({ label: form.name, color: form.color, description: form.description })
}
</script>

<template>
  <div class="p-8">
    <!-- Page Header -->
    <div class="mb-8">
      <h1 class="text-2xl font-semibold text-gray-100 tracking-tight">常用 GIF 圖庫</h1>
      <p class="text-gray-500 text-sm mt-1.5">管理與瀏覽 GIF 動態圖片資源</p>
    </div>

    <!-- Category Filters -->
    <div class="flex flex-wrap items-center gap-2.5 mb-8">
      <button
        v-for="cat in categories"
        :key="cat.label"
        @click="activeCategory = cat.label"
        class="px-5 py-2 rounded-full text-sm font-medium transition-all duration-200 cursor-pointer border"
        :style="activeCategory === cat.label ? categoryStyle(cat.color) : {}"
        :class="activeCategory !== cat.label && 'bg-gray-800 text-gray-500 border-gray-700 hover:text-amber-400 hover:border-amber-400/40'"
      >
        {{ cat.label }}
      </button>

      <div class="w-px h-5 bg-gray-700 mx-0.5" />

      <button
        @click="showCategoryModal = true"
        class="flex items-center gap-1.5 px-4 py-2 rounded-full text-sm font-medium border border-dashed border-gray-600
               text-gray-500 hover:text-amber-400 hover:border-amber-400/50 transition-all duration-200 cursor-pointer"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="w-3.5 h-3.5 flex-shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
        </svg>
        新增類別
      </button>
    </div>

    <!-- Cards Grid -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-5">
      <div
        v-for="gif in filteredGifs()"
        :key="gif.id"
        class="bg-gray-800/60 rounded-2xl overflow-hidden border border-gray-700/60 hover:border-gray-600 transition-all duration-200 hover:shadow-xl hover:shadow-black/30"
      >
        <!-- GIF Preview -->
        <div class="aspect-video bg-gray-900 overflow-hidden">
          <img
            :src="gif.url"
            :alt="gif.title"
            class="w-full h-full object-cover"
          />
        </div>

        <!-- Card Body -->
        <div class="p-4">
          <div class="flex items-center justify-between gap-2 mb-4">
            <h3 class="text-gray-100 font-medium text-sm leading-snug">{{ gif.title }}</h3>
            <span
              class="text-xs px-2.5 py-1 rounded-full flex-shrink-0 font-medium border"
              :style="categoryStyle(colorForCategory(gif.category))"
            >
              {{ gif.category }}
            </span>
          </div>

          <!-- Copy URL Button -->
          <button
            @click="copyUrl(gif.id, gif.url)"
            class="w-full flex items-center justify-center gap-2 py-2.5 px-4 rounded-xl text-sm font-medium transition-all duration-200 cursor-pointer"
            :class="
              copied === gif.id
                ? 'bg-green-500/10 text-green-400 border border-green-500/25'
                : 'bg-amber-400/10 text-amber-400 border border-amber-400/25 hover:bg-amber-400 hover:text-gray-900 hover:shadow-lg hover:shadow-amber-400/20'
            "
          >
            <svg
              v-if="copied !== gif.id"
              xmlns="http://www.w3.org/2000/svg"
              class="w-4 h-4 flex-shrink-0"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M8 16H6a2 2 0 01-2-2V6a2 2 0 012-2h8a2 2 0 012 2v2m-6 12h8a2 2 0 002-2v-8a2 2 0 00-2-2h-8a2 2 0 00-2 2v8a2 2 0 002 2z"
              />
            </svg>
            <svg
              v-else
              xmlns="http://www.w3.org/2000/svg"
              class="w-4 h-4 flex-shrink-0"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M5 13l4 4L19 7"
              />
            </svg>
            {{ copied === gif.id ? '已複製！' : '複製圖片網址' }}
          </button>
        </div>
      </div>
    </div>

    <CategoryModal v-model="showCategoryModal" @confirm="handleCategoryConfirm" />
  </div>
</template>
