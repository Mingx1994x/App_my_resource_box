# 開發規範 (DEVELOPMENT.md)

## 命名規則

### 檔案命名

| 類型 | 規則 | 範例 |
|------|------|------|
| Vue 元件 | PascalCase | `UserProfile.vue`、`NavBar.vue` |
| TypeScript 模組 | camelCase | `useAuth.ts`、`formatDate.ts` |
| CSS/樣式檔 | kebab-case | `global-theme.css` |
| 測試檔 | 與來源同名 + `.spec` | `UserProfile.spec.ts` |
| 計畫檔 | `YYYY-MM-DD-<feature-name>.md` | `2026-04-15-add-router.md` |

### 程式碼命名

| 類型 | 規則 | 範例 |
|------|------|------|
| 元件名稱（template） | PascalCase | `<UserProfile />`、`<NavBar />` |
| `ref`/`reactive` 變數 | camelCase | `const count = ref(0)`、`const userData = reactive({})` |
| computed | camelCase，名詞或形容詞 | `const isLoggedIn = computed(...)` |
| 事件處理函式 | `handle` 前綴 + PascalCase | `handleSubmit`、`handleMenuToggle` |
| Composable | `use` 前綴 + PascalCase | `useAuth`、`useWindowSize` |
| Props 型別 | 元件名 + `Props` | `interface UserProfileProps` |
| Emits 型別 | 元件名 + `Emits` | `interface UserProfileEmits` |
| CSS class（手寫） | kebab-case | `.nav-bar`、`.user-card` |
| CSS ID | kebab-case | `#main-content`、`#app` |
| CSS 自訂屬性 | `--` 前綴 + kebab-case | `--accent-color`、`--text-muted` |

---

## 模組系統

專案使用 `"type": "module"`（package.json），所有 `.ts` 與 `.js` 檔案均為 ES module。

- **import 使用相對路徑**，不需副檔名（TypeScript + Vite bundler 模式自動解析）
- **Vue SFC 中 script setup 的 import** 同樣無需副檔名，但引入 `.vue` 檔案必須帶 `.vue`

```typescript
// 正確
import UserProfile from './components/UserProfile.vue'
import { useAuth } from './composables/useAuth'

// 錯誤：.vue 不能省略
import UserProfile from './components/UserProfile'
```

- **靜態資源 import**（圖片、SVG）：Vite 會回傳帶 hash 的 URL 字串

```typescript
import heroImg from '../assets/hero.png'   // → '/assets/hero-BzCg7x.png'（例）
```

- **`public/` 的資源**不可 import，只能用字串路徑直接引用（`/icons.svg`）

---

## 新增元件步驟

1. 在 `src/components/` 建立 `ComponentName.vue`
2. 使用 `<script setup lang="ts">` 語法
3. 定義 Props 型別（如需要）：

```vue
<script setup lang="ts">
interface Props {
  title: string
  count?: number  // 選填加 ?
}

const props = defineProps<Props>()
</script>
```

4. 定義 Emits（如需要）：

```vue
<script setup lang="ts">
const emit = defineEmits<{
  change: [value: string]   // Vue 3.3+ tuple syntax
  close: []
}>()
</script>
```

5. 在父元件 import 並使用：

```vue
<script setup lang="ts">
import ComponentName from './components/ComponentName.vue'
</script>

<template>
  <ComponentName title="Hello" @change="handleChange" />
</template>
```

**注意**：新增的元件若在父元件中未使用，TypeScript 嚴格模式不會報錯（import 未使用的 .vue 不算 unused），但 `<script setup>` 內未使用的變數會導致建置失敗。

---

## 新增 Composable 步驟

1. 在 `src/composables/` 建立 `useFeatureName.ts`（目前此目錄尚未建立，視需求創建）
2. 函式名稱必須以 `use` 開頭
3. 回傳 `ref`/`reactive`/`computed`，不要直接回傳原始值（保持響應性）

```typescript
// src/composables/useCounter.ts
import { ref } from 'vue'

export function useCounter(initialValue = 0) {
  const count = ref(initialValue)

  function increment() {
    count.value++
  }

  function reset() {
    count.value = initialValue
  }

  return { count, increment, reset }
}
```

---

## 新增全域樣式步驟

全域樣式定義於 `src/style.css`。新增 CSS 自訂屬性：

1. 在 `:root` 區塊加入亮色值
2. 在 `@media (prefers-color-scheme: dark) :root` 加入暗色值
3. 命名格式：`--category-property`（例：`--btn-primary-bg`）

**不要直接在 `style.css` 加入元件專屬樣式**，元件樣式放各 `.vue` 的 `<style scoped>` 區塊，或優先使用 Tailwind utility class。

---

## 環境變數

| 變數 | 用途 | 必要 | 預設值 |
|------|------|------|--------|
| （目前無自訂環境變數） | — | — | — |

Vite 的環境變數規則：
- 放在專案根目錄的 `.env`、`.env.local` 等檔案
- 只有 `VITE_` 前綴的變數才會暴露給前端程式碼
- 在程式碼中以 `import.meta.env.VITE_XXX` 存取
- `.env.local` 已加入 `.gitignore`，適合放機密值

---

## TypeScript 嚴格模式注意事項

`tsconfig.app.json` 啟用了以下嚴格選項：

| 選項 | 行為 |
|------|------|
| `noUnusedLocals` | 宣告但未使用的本地變數 → 建置錯誤 |
| `noUnusedParameters` | 宣告但未使用的函式參數 → 建置錯誤 |
| `erasableSyntaxOnly` | 禁止非標準 TypeScript 語法（如 `const enum`）|
| `noFallthroughCasesInSwitch` | switch case 未加 break/return → 建置錯誤 |

如果需要刻意忽略某個參數，使用 `_` 前綴：

```typescript
// 正確：用 _ 前綴告知 TS 刻意不使用
function handleEvent(_event: MouseEvent) {
  doSomething()
}
```

---

## 計畫歸檔流程

### 1. 建立計畫

新功能開發前，在 `docs/plans/` 建立計畫檔：

```
docs/plans/YYYY-MM-DD-<feature-name>.md
```

例：`docs/plans/2026-04-20-add-vue-router.md`

### 2. 計畫文件結構

```markdown
# [功能名稱] 開發計畫

## User Story
身為 [角色]，我希望 [行為]，以便 [目的]。

## Spec（技術規格）
- 需新增的檔案與其職責
- 需修改的現有檔案
- 關鍵技術決策（例：選用套件、資料結構）

## Tasks
- [ ] 安裝/設定相關依賴
- [ ] 建立核心模組
- [ ] 整合至現有元件
- [ ] 更新文件
```

### 3. 完成後歸檔

功能開發完成後：

1. 將計畫檔移至 `docs/plans/archive/`

   ```bash
   mv docs/plans/2026-04-20-add-vue-router.md docs/plans/archive/
   ```

2. 更新 `docs/FEATURES.md`：將功能狀態從 `[ ]` 改為 `[x]`，補充行為描述
3. 更新 `docs/CHANGELOG.md`：在對應版本區塊加入變更記錄

---

## 建置輸出

```bash
npm run build
```

執行順序：
1. `vue-tsc -b`：對整個專案做 TypeScript 型別檢查（包含 `.vue` 的型別），有錯誤立即停止
2. `vite build`：打包輸出至 `dist/`

`dist/` 目錄結構（建置後）：
```
dist/
├── index.html
├── assets/
│   ├── index-[hash].js    # 打包後的 JS
│   └── index-[hash].css   # 打包後的 CSS（含 Tailwind purged 樣式）
└── [public 目錄的檔案直接複製]
```

Tailwind CSS 4 在建置時會自動 purge 未使用的 utility class，只輸出實際用到的 CSS。
