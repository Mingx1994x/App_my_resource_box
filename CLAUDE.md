# CLAUDE.md

## 專案概述

**ya-resource-frontend-app** — Vue 3 + TypeScript + Vite + Tailwind CSS 4 的單頁應用程式（SPA）。使用 `<script setup>` Composition API，TypeScript 嚴格模式，以 Vite 作為建置工具。

## 常用指令

```bash
npm run dev       # 啟動 Vite 開發伺服器（HMR 熱更新，預設 http://localhost:5173）
npm run build     # vue-tsc 型別檢查後進行正式環境建置，輸出至 dist/
npm run preview   # 在本機以靜態伺服器預覽 dist/ 建置結果
```

目前未設定 linting、格式化或測試指令。

## 關鍵規則

- **Composition API only**：所有元件使用 `<script setup lang="ts">`，不使用 Options API
- **TypeScript 嚴格模式**：`noUnusedLocals`、`noUnusedParameters` 均為 true，宣告未使用的變數會導致建置失敗
- **靜態資源兩種路徑**：模組化引入放 `src/assets/`；需以 URL 直接存取（如 SVG sprite）放 `public/`
- **樣式優先使用 Tailwind**：全域 CSS 自訂屬性定義於 `src/style.css`，元件樣式優先使用 Tailwind utility class，複雜動畫或特殊 CSS 才使用 `<style>` 區塊
- **計畫歸檔**：功能開發使用 `docs/plans/` 記錄計畫；完成後移至 `docs/plans/archive/`，並更新 `docs/FEATURES.md` 與 `docs/CHANGELOG.md`

## 詳細文件

- [docs/README.md](./docs/README.md) — 項目介紹與快速開始
- [docs/ARCHITECTURE.md](./docs/ARCHITECTURE.md) — 架構、目錄結構、資料流
- [docs/DEVELOPMENT.md](./docs/DEVELOPMENT.md) — 開發規範、命名規則
- [docs/FEATURES.md](./docs/FEATURES.md) — 功能列表與完成狀態
- [docs/TESTING.md](./docs/TESTING.md) — 測試規範與指南
- [docs/CHANGELOG.md](./docs/CHANGELOG.md) — 更新日誌
