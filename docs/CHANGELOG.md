# 更新日誌 (CHANGELOG.md)

本日誌格式依照 [Keep a Changelog](https://keepachangelog.com/zh-TW/1.0.0/)，版本號依照 [Semantic Versioning](https://semver.org/lang/zh-TW/)。

---

## [Unreleased]

（下個版本的變更會記錄在此）

---

## [0.2.0] — 2026-04-15

### 新增

- **後台管理介面**（`src/App.vue`）
  - 全螢幕暗黑模式佈局（`bg-[#0d1117]`），不再跟隨系統偏好
  - 左側可收合 Sidebar（`w-60` ↔ `w-16`），含展開/收起切換按鈕
  - Sidebar 品牌標題、選單項目支援 active 高亮與收起後純 icon 模式

- **常用 GIF 圖庫頁面**（`src/components/GifGallery.vue`）
  - 類別篩選按鈕（所有 / 可愛 / 常用），active 色彩與卡片標籤統一：
    - 所有 → amber-400、可愛 → pink-400、常用 → sky-400
  - GIF 卡片網格（1 欄 / sm:2 欄 / lg:3 欄），每張卡片包含：
    - GIF 直接預覽（`aspect-video`，以 `gif.url` 作為 `<img src>`）
    - 圖片標題、類別標籤
    - 複製圖片網址按鈕（amber-400 主色，hover 填滿，複製後轉綠色「已複製！」2 秒）

### 變更

- **`src/App.vue`**：由原本僅渲染 `HelloWorld` 改為後台管理 Shell，包含 Sidebar + 主內容區
- **`src/style.css`**：`#app` 樣式從限寬置中佈局改為全寬（`width: 100%`），`body` 背景預設為暗色（`#0d1117`）

---

## [0.1.0] — 2026-04-15

### 新增

- **Vue 3 + TypeScript + Vite 專案初始化**
  - 使用 `<script setup>` Composition API 語法
  - TypeScript 6.0.2 嚴格模式（`noUnusedLocals`、`noUnusedParameters`、`erasableSyntaxOnly`、`noFallthroughCasesInSwitch`）
  - Vite 8.0.4 開發伺服器（HMR）與建置工具
  - 分離 tsconfig：`tsconfig.app.json`（src/）與 `tsconfig.node.json`（vite.config.ts）

- **Tailwind CSS 4 整合**
  - 安裝 `tailwindcss@4.2.2` 與 `@tailwindcss/vite@4.2.2`
  - 在 `vite.config.ts` 加入 `tailwindcss()` plugin（位於 `vue()` 之前）
  - 在 `src/style.css` 頂端加入 `@import "tailwindcss"`
  - 無需 `tailwind.config.js`，以 CSS 驅動設定

- **全域樣式系統**（`src/style.css`）
  - 10 個 CSS 自訂屬性 design token（text、bg、border、accent 等系列）
  - `@media (prefers-color-scheme: dark)` 深色模式自動切換
  - 響應式斷點：`max-width: 1024px`
  - `#app` 主容器（最大寬 1126px）、`#center`、`#next-steps`、`#spacer` 等佈局

- **HelloWorld.vue 展示元件**
  - Hero 圖區塊（hero.png + vue.svg 3D 旋轉 + vite.svg 3D 旋轉）
  - 互動計數器（`ref` 驅動，每次點擊 +1）
  - Next Steps 區塊（Documentation 欄、Social 欄）

- **SVG Sprite**（`public/icons.svg`）
  - 包含 6 個圖標：`documentation-icon`、`social-icon`、`github-icon`、`discord-icon`、`x-icon`、`bluesky-icon`

- **Claude Code 設定**（`.claude/setting.json`）
  - 沙箱模式啟用，`autoAllowBashIfSandboxed: true`
  - 網路白名單：npm registry、GitHub 相關域名
  - 禁止指令：`rm -rf *`、`sudo *`、`git push *`、`git reset --hard *`

- **專案文件體系**（`docs/`）
  - `docs/README.md`：技術棧、快速開始、文件索引
  - `docs/ARCHITECTURE.md`：目錄結構、啟動流程、樣式系統、元件架構
  - `docs/DEVELOPMENT.md`：命名規則、新增元件/composable 步驟、計畫歸檔流程
  - `docs/FEATURES.md`：功能清單與行為描述
  - `docs/TESTING.md`：測試規範（Vitest 設定建議）
  - `docs/CHANGELOG.md`：本檔案
  - `docs/plans/`：開發計畫目錄，`docs/plans/archive/` 已完成計畫歸檔
