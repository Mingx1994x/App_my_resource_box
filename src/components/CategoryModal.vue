<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { ColorPicker } from 'vue3-colorpicker'
import 'vue3-colorpicker/style.css'

interface CategoryForm { name: string; color: string; description: string }

const DEFAULT_COLOR = '#F59E0B'

const props = defineProps<{ modelValue: boolean }>()
const emit = defineEmits<{
  'update:modelValue': [value: boolean]
  confirm: [form: CategoryForm]
}>()

const form = ref<CategoryForm>({ name: '', color: DEFAULT_COLOR, description: '' })
const nameError = ref('')

function validateName() {
  nameError.value = form.value.name.trim() === '' ? '請輸入標籤名稱' : ''
}

function resetAndClose() {
  form.value = { name: '', color: DEFAULT_COLOR, description: '' }
  nameError.value = ''
  emit('update:modelValue', false)
}

function handleSubmit() {
  validateName()
  if (nameError.value) return
  emit('confirm', { ...form.value })
  resetAndClose()
}

function handleBackdropClick(e: MouseEvent) {
  if (e.target === e.currentTarget) resetAndClose()
}

function handleKeydown(e: KeyboardEvent) {
  if (e.key === 'Escape' && props.modelValue) resetAndClose()
}
onMounted(() => document.addEventListener('keydown', handleKeydown))
onUnmounted(() => document.removeEventListener('keydown', handleKeydown))
</script>

<template>
  <Teleport to="body">
    <Transition name="modal-fade">
      <div
        v-if="modelValue"
        class="fixed inset-0 z-50 flex items-center justify-center bg-black/60 backdrop-blur-sm px-4"
        @click="handleBackdropClick"
      >
        <!-- Modal Box -->
        <div
          class="modal-box w-full max-w-md bg-[#161b22] border border-gray-700/60 rounded-2xl shadow-2xl shadow-black/50 flex flex-col"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-title"
        >
          <!-- Header -->
          <div class="flex items-center justify-between px-6 py-4 border-b border-gray-800">
            <h2 id="modal-title" class="text-gray-100 font-semibold text-base tracking-tight">
              新增類別
            </h2>
            <button
              @click="resetAndClose"
              class="text-gray-500 hover:text-gray-300 transition-colors p-1.5 rounded-lg hover:bg-gray-800 cursor-pointer"
              aria-label="關閉"
            >
              <svg xmlns="http://www.w3.org/2000/svg" class="w-4 h-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
              </svg>
            </button>
          </div>

          <!-- Form Body -->
          <form @submit.prevent="handleSubmit" class="px-6 py-5 space-y-5">

            <!-- 標籤名稱 -->
            <div>
              <label class="block text-sm font-medium text-gray-300 mb-1.5">
                標籤名稱 <span class="text-red-400">*</span>
              </label>
              <input
                v-model="form.name"
                @blur="validateName"
                type="text"
                placeholder="例：工作、搞笑、節日…"
                class="w-full px-3.5 py-2.5 bg-gray-800/80 border border-gray-700 rounded-xl text-sm text-gray-100
                       placeholder-gray-600 focus:outline-none focus:border-amber-400/60 focus:ring-1 focus:ring-amber-400/30
                       transition-colors"
              />
              <p v-if="nameError" class="mt-1.5 text-xs text-red-400">{{ nameError }}</p>
            </div>

            <!-- 顏色 -->
            <div>
              <label class="block text-sm font-medium text-gray-300 mb-3">顏色</label>

              <!-- Color Picker -->
              <div class="color-picker-wrap">
                <ColorPicker
                  v-model:pureColor="form.color"
                  format="hex"
                  shape="circle"
                  :disableAlpha="true"
                />
              </div>

              <!-- Hex 顯示 + 預覽 -->
              <div class="flex items-center gap-3 mt-3">
                <!-- 顏色預覽圓 -->
                <div
                  class="w-8 h-8 rounded-full flex-shrink-0 border-2 border-white/10 shadow-sm"
                  :style="{ backgroundColor: form.color }"
                />

                <!-- Read-only hex input -->
                <div class="relative flex-1">
                  <span class="absolute left-3 top-1/2 -translate-y-1/2 text-gray-500 text-sm font-mono select-none">#</span>
                  <input
                    type="text"
                    :value="form.color.replace('#', '')"
                    readonly
                    class="w-full pl-7 pr-3.5 py-2.5 bg-gray-800/80 border border-gray-700 rounded-xl text-sm text-gray-300
                           font-mono tracking-widest cursor-default select-all focus:outline-none"
                  />
                </div>

                <!-- 預覽 pill -->
                <span
                  class="text-xs px-2.5 py-1.5 rounded-full font-medium border flex-shrink-0"
                  :style="{
                    color: form.color,
                    backgroundColor: form.color + '26',
                    borderColor: form.color + '40',
                  }"
                >
                  預覽
                </span>
              </div>
            </div>

            <!-- 內容說明 -->
            <div>
              <label class="block text-sm font-medium text-gray-300 mb-1.5">內容說明</label>
              <textarea
                v-model="form.description"
                rows="3"
                placeholder="簡短描述此類別的用途…"
                class="w-full px-3.5 py-2.5 bg-gray-800/80 border border-gray-700 rounded-xl text-sm text-gray-100
                       placeholder-gray-600 focus:outline-none focus:border-amber-400/60 focus:ring-1 focus:ring-amber-400/30
                       transition-colors resize-none"
              />
            </div>

            <!-- Footer Buttons -->
            <div class="flex gap-3 pt-1">
              <button
                type="button"
                @click="resetAndClose"
                class="flex-1 py-2.5 px-4 rounded-xl text-sm font-medium border border-gray-700
                       text-gray-400 hover:text-gray-200 hover:border-gray-600 transition-all duration-200 cursor-pointer"
              >
                取消
              </button>
              <button
                type="submit"
                class="flex-1 py-2.5 px-4 rounded-xl text-sm font-medium
                       bg-amber-400/10 text-amber-400 border border-amber-400/25
                       hover:bg-amber-400 hover:text-gray-900 hover:shadow-lg hover:shadow-amber-400/20
                       transition-all duration-200 cursor-pointer"
              >
                新增類別
              </button>
            </div>
          </form>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<style scoped>
.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: opacity 0.2s ease;
}
.modal-fade-enter-from,
.modal-fade-leave-to {
  opacity: 0;
}
.modal-fade-enter-active .modal-box,
.modal-fade-leave-active .modal-box {
  transition: opacity 0.2s ease, transform 0.2s ease;
}
.modal-fade-enter-from .modal-box,
.modal-fade-leave-to .modal-box {
  opacity: 0;
  transform: translateY(-8px) scale(0.97);
}

/* 讓 vue3-colorpicker 融入深色主題 */
.color-picker-wrap :deep(.vc-colorpicker) {
  background: transparent !important;
  box-shadow: none !important;
  padding: 0 !important;
  width: 100% !important;
}
.color-picker-wrap :deep(.vc-colorpicker--container) {
  width: 100% !important;
}
.color-picker-wrap :deep(.vc-saturation) {
  border-radius: 10px !important;
  overflow: hidden;
}
.color-picker-wrap :deep(.vc-hue),
.color-picker-wrap :deep(.vc-alpha) {
  border-radius: 6px !important;
}
.color-picker-wrap :deep(.vc-input__label),
.color-picker-wrap :deep(.vc-input__input) {
  display: none !important;
}
.color-picker-wrap :deep(.vc-input__div) {
  display: none !important;
}
</style>
