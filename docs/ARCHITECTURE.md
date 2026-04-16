# 架構文件 (ARCHITECTURE.md)

## 技術決策摘要

以下是影響整個專案開發的關鍵技術決策，**不了解這些會影響其他模組的整合**：

1. **Tailwind CSS 4 透過 Vite plugin 整合**：不需要 `tailwind.config.js`，設定改以 CSS `@import "tailwindcss"` 驅動；Tailwind plugin 必須在 `vue()` 之前載入
2. **TypeScript 分兩份 tsconfig**：`src/` 使用 `tsconfig.app.json`（DOM 型別），`vite.config.ts` 使用 `tsconfig.node.json`（Node 型別）；兩者不共用，不可交叉引入
3. **`noUnusedLocals` + `noUnusedParameters` 為 true**：任何宣告未使用的變數、函式參數都會讓 `npm run build` 失敗，開發中注意及時清理
4. **SVG 圖標使用 sprite 方式**：`public/icons.svg` 是一個含多個 `<symbol>` 的 SVG sprite，在元件中以 `<use href="/icons.svg#icon-id">` 引用，不需 JavaScript import
5. **無路由、無全域狀態管理**：目前未安裝 Vue Router 與 Pinia，狀態在元件層級以 `ref`/`reactive` 管理

---

## 目錄結構

```
ya-resource-frontend-app/
├── index.html                   # HTML 外殼，掛載點 #app，以 ES module 載入 /src/main.ts
├── vite.config.ts               # Vite 設定，載入 tailwindcss() + vue() plugin
├── tsconfig.json                # 根設定，僅 references 指向兩份子設定
├── tsconfig.app.json            # src/ 的 TypeScript 設定（DOM、嚴格模式、vite/client 型別）
├── tsconfig.node.json           # vite.config.ts 的 TypeScript 設定（Node、ES2023）
├── package.json                 # npm 依賴與 scripts
├── package-lock.json            # 鎖定依賴版本
├── CLAUDE.md                    # Claude Code 工作指引
│
├── public/                      # 靜態資源，直接以根路徑 / 提供，不經 Vite 處理
│   ├── favicon.svg              # 網站圖標（<link rel="icon">）
│   └── icons.svg                # SVG sprite，含多個 <symbol>（documentation-icon、social-icon、github-icon、discord-icon、x-icon、bluesky-icon）
│
├── src/
│   ├── main.ts                  # Vue 應用進入點：import style.css → createApp(App).mount('#app')
│   ├── App.vue                  # 根元件，後台管理 Shell（Sidebar + 主內容區）
│   ├── style.css                # 全域樣式：@import "tailwindcss" + CSS 自訂屬性（#app 全寬）
│   ├── assets/                  # 以 ES module 方式 import 的靜態資源（Vite 會處理 hash）
│   │   ├── hero.png             # Hero 區塊的背景圖片（170×179 px）
│   │   ├── vite.svg             # Vite 標誌
│   │   └── vue.svg              # Vue 標誌
│   └── components/
│       ├── HelloWorld.vue       # 原始展示元件（Hero 圖、計數器按鈕、Next Steps）
│       ├── GifGallery.vue       # 常用 GIF 圖庫頁面（類別篩選、卡片網格、複製網址）
│       └── CategoryModal.vue    # 新增類別 Modal（vue3-colorpicker、hex 顯示、表單驗證）
│
├── docs/                        # 專案文件目錄
│   ├── README.md
│   ├── ARCHITECTURE.md          # 本文件
│   ├── DEVELOPMENT.md
│   ├── FEATURES.md
│   ├── TESTING.md
│   ├── CHANGELOG.md
│   └── plans/                   # 進行中的開發計畫
│       └── archive/             # 已完成計畫歸檔
│
└── .claude/
    └── setting.json             # Claude Code 沙箱設定（權限白名單、網路域名白名單）
```

---

## 啟動流程

```
瀏覽器請求 http://localhost:5173
    ↓
index.html 被 Vite 提供
    ↓
<script type="module" src="/src/main.ts"> 被執行
    ↓
main.ts:
  import './style.css'           ← Tailwind 全域樣式注入（@import "tailwindcss" + #app 全寬）
  import App from './App.vue'    ← 根元件載入
  createApp(App).mount('#app')   ← Vue 應用掛載至 index.html 的 <div id="app">
    ↓
App.vue render（後台管理 Shell）:
  const sidebarOpen = ref(true)  ← Sidebar 展開/收起狀態
  <aside>                        ← 可收合 Sidebar（w-60 / w-16）
  <main>
    <GifGallery />               ← 常用 GIF 圖庫頁面
  </main>
    ↓
GifGallery.vue:
  const categories = ref<Category[]>(...)  ← 類別清單（含 hex 色碼），可動態新增
  const activeCategory = ref('所有')       ← 類別篩選狀態
  const copied = ref<number | null>(null)  ← 複製回饋狀態
  const showCategoryModal = ref(false)     ← Modal 開關
  categoryStyle(hex) → inline style        ← hex 轉半透明色彩 style
  render: 頁面標題 + 類別篩選按鈕列 + GIF 卡片網格 + CategoryModal
```

---

## 元件架構

```
App.vue (根元件 — 後台管理 Shell)
├── <aside>  ← 可收合 Sidebar
│   ├── 品牌標題「後台管理」（展開時顯示）
│   ├── 展開/收起切換按鈕（SVG chevron icon）
│   └── <nav> 選單項目（目前只有「常用 GIF 圖庫」）
└── <main>
    └── GifGallery.vue  ← 常用 GIF 圖庫頁面
        ├── 頁面標題 + 副標
        ├── 類別篩選按鈕列 + 「新增類別」按鈕
        ├── GIF 卡片網格
        │   └── 卡片 × N
        │       ├── <img> GIF 預覽（aspect-video）
        │       ├── 圖片標題
        │       ├── 類別標籤（hex inline style）
        │       └── 複製網址按鈕
        └── CategoryModal.vue  ← <Teleport to="body">，渲染至 #app 外層
            ├── 標籤名稱 input（必填驗證）
            ├── 顏色選取器（vue3-colorpicker）+ hex 唯讀 input + 預覽 pill
            └── 內容說明 textarea（選填）
```

---

## 樣式系統

### CSS 自訂屬性（Design Tokens）

定義於 `src/style.css` 的 `:root`，全專案共用：

| 變數名 | 用途 | 亮色值 | 暗色值 |
|--------|------|--------|--------|
| `--text` | 正文顏色 | `#6b6375` | `#9ca3af` |
| `--text-h` | 標題/強調文字 | `#08060d` | `#f3f4f6` |
| `--bg` | 頁面背景 | `#fff` | `#16171d` |
| `--border` | 框線、分隔線 | `#e5e4e7` | `#2e303a` |
| `--code-bg` | `<code>` 背景 | `#f4f3ec` | `#1f2028` |
| `--accent` | 強調色（紫）| `#aa3bff` | `#c084fc` |
| `--accent-bg` | 強調色背景 | `rgba(170,59,255,0.1)` | `rgba(192,132,252,0.15)` |
| `--accent-border` | 強調色框線 | `rgba(170,59,255,0.5)` | `rgba(192,132,252,0.5)` |
| `--social-bg` | 社群按鈕背景 | `rgba(244,243,236,0.5)` | `rgba(47,48,58,0.5)` |
| `--shadow` | 卡片陰影 | `rgba(0,0,0,0.1)...` | `rgba(0,0,0,0.4)...` |
| `--sans` | 主字體 | `system-ui, 'Segoe UI', Roboto, sans-serif` | 同左 |
| `--heading` | 標題字體 | `system-ui, 'Segoe UI', Roboto, sans-serif` | 同左 |
| `--mono` | 等寬字體 | `ui-monospace, Consolas, monospace` | 同左 |

### 深色模式

透過 `@media (prefers-color-scheme: dark)` 覆寫 CSS 自訂屬性，不需 JavaScript 或 class toggle。另對 `#social .button-icon` 加 `filter: invert(1) brightness(2)` 使深色模式下 SVG 圖標變白。

### 響應式斷點

只有一個斷點：`max-width: 1024px`，影響：
- `:root` font-size 從 18px 降為 16px
- `h1` 從 56px 降為 36px，margin 從 32px 降為 20px
- `h2` 從 24px 降為 20px
- `#center` 加 `padding: 32px 20px 24px`，gap 從 25px 降為 18px
- `#next-steps` 從橫排改為直排（flex-direction: column），文字置中
- `#next-steps > div` padding 從 32px 降為 `24px 20px`
- `#next-steps ul` 改為 wrap，每個 `li` 佔 50% 寬
- `#docs` 在寬螢幕有右框線，小螢幕改為下框線
- `#spacer` 高度從 88px 降為 48px

### 佈局

`#app` 是主容器：最大寬 1126px，水平置中，左右各有 1px border，最小高度 100svh（支援 iOS Safari 動態視口），垂直 flex column。

### Tailwind CSS 4 整合

在 `src/style.css` 第一行 `@import "tailwindcss"` 引入，由 `@tailwindcss/vite` plugin 處理（在 `vite.config.ts` 中排在 `vue()` 之前）。Tailwind class 可直接在所有 `.vue` 模板中使用，無需額外設定。

---

## Vite 設定

```typescript
// vite.config.ts
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [
    tailwindcss(),  // 必須在 vue() 之前，負責處理 @import "tailwindcss"
    vue(),          // 處理 .vue SFC 檔案
  ],
})
```

**重要**：Tailwind plugin 順序不可調換，必須在 Vue plugin 之前。

---

## Claude Code 沙箱設定（.claude/setting.json）

```json
{
  "permissions": {
    "allow": ["Edit(~/.npm/**)", "Read(~/.npm/**)" ],
    "deny": ["Bash(rm -rf *)", "Bash(sudo *)", "Bash(git push *)", "Bash(git reset --hard *)"]
  },
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": true,
    "network": {
      "allowLocalBinding": true,
      "allowedDomains": ["registry.npmjs.org", "*.npmjs.org", "*.github.com", "github.com", "*.githubusercontent.com"]
    }
  }
}
```

沙箱啟用後，`autoAllowBashIfSandboxed: true` 讓沙箱內的 Bash 指令自動核准，網路僅允許 npm registry 與 GitHub 域名。
