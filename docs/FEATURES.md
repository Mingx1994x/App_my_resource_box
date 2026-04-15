# 功能清單 (FEATURES.md)

最後更新：2026-04-15

## 功能狀態總覽

| 功能 | 狀態 | 說明 |
|------|------|------|
| Vite 開發環境 | ✅ 完成 | HMR、TypeScript、Vue SFC |
| Tailwind CSS 4 整合 | ✅ 完成 | @tailwindcss/vite plugin |
| 深色模式 | ✅ 完成 | 系統偏好自動切換 |
| 響應式佈局 | ✅ 完成 | 1024px 斷點 |
| 互動計數器 | ✅ 完成 | HelloWorld 展示元件 |
| Vue Router | ❌ 未安裝 | 目前為單頁，無路由需求 |
| Pinia 狀態管理 | ❌ 未安裝 | 狀態在元件層管理 |
| 測試框架 | ❌ 未設定 | 詳見 TESTING.md |

---

## 功能詳細說明

### Vite 開發環境

**狀態**：✅ 完成

Vite 8.0.4 作為開發伺服器與建置工具。

**行為描述**：
- `npm run dev` 啟動後，預設監聽 `http://localhost:5173`，若該 port 被佔用則自動遞增（5174、5175...）
- 儲存 `.vue` 或 `.ts` 檔案後，瀏覽器在不重新整理的情況下熱更新（HMR）
- 儲存 `src/style.css` 後，樣式即時更新，不影響元件狀態（計數器數字不重置）
- `npm run build` 先執行 `vue-tsc -b` 型別檢查，有 TS 錯誤則中止，不會產出 `dist/`

**非標準行為**：
- `public/` 目錄的檔案在開發時以根路徑直接提供，不經 Vite 的模組處理，也不會被 hash
- `src/assets/` 的檔案在建置時會被加上 content hash（例：`hero.png` → `hero-BzCg7x.png`），用於長期快取

---

### Tailwind CSS 4 整合

**狀態**：✅ 完成

使用 `@tailwindcss/vite` 4.2.2 plugin 整合，透過 `@import "tailwindcss"` 在 CSS 中啟用。

**行為描述**：
- 所有 `.vue` 模板中可直接使用 Tailwind utility class，無需額外設定或 import
- Tailwind 4 不需要 `tailwind.config.js`，自訂 token 可透過 CSS 變數或 `@theme` 指令在 `style.css` 中定義
- 建置時自動 purge，只輸出實際使用的 class 對應的 CSS
- `@import "tailwindcss"` 必須是 `src/style.css` 的第一行，否則可能影響 CSS 載入順序

**與現有 CSS 共存**：
- 現有的 CSS 自訂屬性（`--text`、`--accent` 等）與 Tailwind 並存，互不衝突
- 若 Tailwind 的 preflight（reset）與現有樣式衝突，可用 `@layer base` 覆寫或在 Tailwind 4 中關閉 preflight

---

### 深色模式

**狀態**：✅ 完成

完全透過 CSS 實現，不需 JavaScript。

**行為描述**：
- 自動偵測作業系統的深色模式偏好（macOS、Windows、iOS 等設定）
- 切換機制：`@media (prefers-color-scheme: dark)` 覆寫 `:root` 的 CSS 自訂屬性值
- 受影響的 token：`--text`、`--text-h`、`--bg`、`--border`、`--code-bg`、`--accent`、`--accent-bg`、`--accent-border`、`--social-bg`、`--shadow`（共 10 個變數）
- 特殊處理：`#social .button-icon`（SVG 圖標）在深色模式加 `filter: invert(1) brightness(2)` 使黑色圖標變白

**限制**：
- 目前不支援使用者手動切換（無 toggle 按鈕），完全跟隨系統設定

---

### 響應式佈局

**狀態**：✅ 完成

**斷點**：只有 `max-width: 1024px` 一個斷點。

**行為描述**：

在寬螢幕（> 1024px）：
- `#app`：最大寬 1126px，水平置中，左右各 1px border
- `#next-steps`：橫向排列（`flex-direction: row`），左欄（#docs）有右 border 分隔
- `#next-steps ul`：連結水平排列

在窄螢幕（≤ 1024px）：
- 整體字型從 18px 降為 16px
- `h1`：56px → 36px，margin 32px → 20px
- `h2`：24px → 20px
- `#center`：加上 `padding: 32px 20px 24px`，gap 從 25px 降至 18px
- `#next-steps`：改為垂直排列（`flex-direction: column`），文字置中；`#docs` 改下 border 代替右 border
- `#next-steps ul`：連結以 wrap 換行，每個 `<li>` 佔 50%（兩欄格局），`<a>` 全寬置中
- `#spacer`：88px → 48px

---

### 互動計數器

**狀態**：✅ 完成（展示用元件）

**行為描述**：
- 位於 `HelloWorld.vue` 的 `<section id="center">` 中
- 渲染為 `<button class="counter">Count is {{ count }}</button>`
- 初始值：`const count = ref(0)`（元件掛載時為 0）
- 點擊行為：`@click="count++"` 每次點擊 +1，無上限、無重置機制
- 狀態作用域：僅在 `HelloWorld.vue` 元件內，不與其他元件共享
- HMR 行為：編輯 `HelloWorld.vue` 儲存後，若 Vite 進行 Hot Module Replacement，count 狀態**保留**；若觸發整頁重整，狀態**重置**為 0

**樣式行為**：
- 正常：有 2px transparent border + accent 色文字 + accent 背景
- `:hover`：border 顯示 `--accent-border` 色，transition 0.3s
- `:focus-visible`（鍵盤導航）：2px solid `--accent` outline，offset 2px

---

### SVG Sprite 系統

**狀態**：✅ 完成

`public/icons.svg` 是一個包含多個 `<symbol>` 的 SVG sprite。

**可用圖標 ID**：

| ID | 使用位置 |
|----|--------|
| `documentation-icon` | `#docs` 區塊標題旁 |
| `social-icon` | `#social` 區塊標題旁 |
| `github-icon` | GitHub 連結按鈕 |
| `discord-icon` | Discord 連結按鈕 |
| `x-icon` | X.com 連結按鈕 |
| `bluesky-icon` | Bluesky 連結按鈕 |

**使用方式**：

```html
<!-- 在 .vue 模板中直接用字串路徑，不需 import -->
<svg class="icon" role="presentation" aria-hidden="true">
  <use href="/icons.svg#documentation-icon"></use>
</svg>
```

**注意**：這些 SVG 圖標是裝飾性的（`aria-hidden="true"`），無障礙文字由周圍的文字內容提供。

---

## 待開發功能（建議）

以下為可能的下一步功能方向，尚未確認優先順序：

| 功能 | 說明 |
|------|------|
| Vue Router | 多頁面路由，需安裝 `vue-router@4` |
| Pinia | 跨元件狀態管理 |
| ESLint + Prettier | 程式碼品質與格式化 |
| Vitest | 單元測試框架 |
| 自訂 Tailwind Theme | 以 `@theme` 指令整合現有 CSS 變數 |
| 手動深色模式切換 | 以 `class="dark"` + LocalStorage 實現 |
