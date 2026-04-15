# ya-resource-frontend-app

Vue 3 + TypeScript + Vite + Tailwind CSS 4 的單頁應用程式（SPA）。

## 技術棧

| 分類 | 技術 | 版本 |
|------|------|------|
| 框架 | Vue 3 (`<script setup>` Composition API) | ^3.5.32 |
| 語言 | TypeScript（嚴格模式） | ~6.0.2 |
| 建置工具 | Vite | ^8.0.4 |
| CSS 框架 | Tailwind CSS 4（@tailwindcss/vite 整合） | ^4.2.2 |
| 型別檢查 | vue-tsc | ^3.2.6 |

## 快速開始

```bash
# 1. 安裝依賴
npm install

# 2. 啟動開發伺服器（HMR）
npm run dev
# → http://localhost:5173

# 3. 型別檢查 + 正式建置
npm run build
# → 輸出至 dist/

# 4. 本機預覽建置結果
npm run preview
```

## 常用指令

| 指令 | 說明 |
|------|------|
| `npm run dev` | 啟動 Vite 開發伺服器，支援 HMR 熱更新 |
| `npm run build` | 先執行 `vue-tsc -b` 型別檢查，再以 Vite 建置 `dist/` |
| `npm run preview` | 以靜態伺服器本機預覽 `dist/` 目錄 |

## 文件索引

| 文件 | 說明 |
|------|------|
| [ARCHITECTURE.md](./ARCHITECTURE.md) | 目錄結構、啟動流程、元件架構、樣式系統 |
| [DEVELOPMENT.md](./DEVELOPMENT.md) | 開發規範、命名規則、新增元件步驟、計畫歸檔流程 |
| [FEATURES.md](./FEATURES.md) | 功能清單、完成狀態、行為描述 |
| [TESTING.md](./TESTING.md) | 測試規範（目前未設定測試框架） |
| [CHANGELOG.md](./CHANGELOG.md) | 版本更新日誌 |
| [../CLAUDE.md](../CLAUDE.md) | Claude Code 工作指引 |
