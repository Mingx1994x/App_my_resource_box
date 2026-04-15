# CLAUDE.md

本檔案為 Claude Code (claude.ai/code) 在此專案中工作時提供指引。

## 指令

```bash
npm run dev       # 啟動 Vite 開發伺服器（支援 HMR 熱更新）
npm run build     # 型別檢查（vue-tsc）後進行正式環境建置
npm run preview   # 在本機預覽正式環境建置結果
```

目前未設定任何 linting、格式化或測試指令。

## 架構

本專案為 **Vue 3 + TypeScript + Vite** 的單頁應用程式（SPA），技術棧如下：

- **Vue 3**：使用 `<script setup>` Composition API 語法
- **TypeScript**：啟用嚴格模式（強制 `noUnusedLocals`、`noUnusedParameters`）
- **Vite**：作為建置工具與開發伺服器

### 進入點

- `index.html` — HTML 外殼，以 ES module 方式載入 `/src/main.ts`
- `src/main.ts` — 建立 Vue 應用程式並掛載至 `#app`
- `src/App.vue` — 根元件

### TypeScript 設定

`tsconfig.json` 引用了兩份獨立設定：
- `tsconfig.app.json` — 涵蓋 `src/`（嚴格模式、Vue 感知、DOM 型別）
- `tsconfig.node.json` — 涵蓋 `vite.config.ts`（Node 型別、ES2023）

### 樣式

全域樣式與 CSS 自訂屬性定義於 `src/style.css`。深色模式透過 `@media (prefers-color-scheme: dark)` 處理。元件本地樣式放在各 `.vue` 檔案的 `<style>` 區塊中。

### 靜態資源

以模組方式匯入的靜態資源應放在 `src/assets/`。`public/` 中的檔案會直接以根路徑提供（例如 `public/icons.svg` 可透過 `/icons.svg` 存取，並以 SVG `<use>` 方式引用）。

### 狀態管理與路由

未安裝路由器或全域狀態管理（Vue Router / Pinia）。狀態使用 Composition API 的 `ref`／`reactive` 在元件層級管理。
