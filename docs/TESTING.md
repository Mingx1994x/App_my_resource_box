# 測試規範 (TESTING.md)

## 目前狀態

**測試框架尚未設定。** 目前 `package.json` 中無 `test` script，也未安裝任何測試相關依賴。

手動驗證流程：
1. `npm run dev` → 瀏覽器確認畫面正常
2. `npm run build` → 確認 TypeScript 型別檢查通過 + 建置成功
3. 點擊計數器確認互動正常

---

## 建議的測試框架設定

### 推薦：Vitest + Vue Test Utils

Vitest 與 Vite 共用設定，是 Vue 3 生態最自然的選擇。

**安裝步驟**（尚未執行）：

```bash
npm install -D vitest @vue/test-utils @vitejs/plugin-vue jsdom
```

**vite.config.ts 修改**：

```typescript
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'
import tailwindcss from '@tailwindcss/vite'

export default defineConfig({
  plugins: [tailwindcss(), vue()],
  test: {
    environment: 'jsdom',
    globals: true,
  },
})
```

**package.json 新增 script**：

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run"
  }
}
```

---

## 測試檔案規範（設定後適用）

### 命名規則

| 被測對象 | 測試檔命名 | 位置 |
|---------|-----------|------|
| `src/components/HelloWorld.vue` | `HelloWorld.spec.ts` | `src/components/__tests__/` 或同目錄 |
| `src/composables/useCounter.ts` | `useCounter.spec.ts` | `src/composables/__tests__/` |

建議將測試檔放在 `__tests__/` 子目錄，與原始檔同層：

```
src/
├── components/
│   ├── HelloWorld.vue
│   └── __tests__/
│       └── HelloWorld.spec.ts
└── composables/
    ├── useCounter.ts
    └── __tests__/
        └── useCounter.spec.ts
```

### 撰寫元件測試範例

```typescript
// src/components/__tests__/HelloWorld.spec.ts
import { mount } from '@vue/test-utils'
import { describe, it, expect } from 'vitest'
import HelloWorld from '../HelloWorld.vue'

describe('HelloWorld', () => {
  it('renders count starting at 0', () => {
    const wrapper = mount(HelloWorld)
    expect(wrapper.find('.counter').text()).toBe('Count is 0')
  })

  it('increments count on click', async () => {
    const wrapper = mount(HelloWorld)
    const button = wrapper.find('.counter')
    await button.trigger('click')
    expect(button.text()).toBe('Count is 1')
  })

  it('increments count multiple times', async () => {
    const wrapper = mount(HelloWorld)
    const button = wrapper.find('.counter')
    await button.trigger('click')
    await button.trigger('click')
    await button.trigger('click')
    expect(button.text()).toBe('Count is 3')
  })
})
```

### 撰寫 Composable 測試範例

```typescript
// src/composables/__tests__/useCounter.spec.ts
import { describe, it, expect } from 'vitest'
import { useCounter } from '../useCounter'

describe('useCounter', () => {
  it('starts with default value 0', () => {
    const { count } = useCounter()
    expect(count.value).toBe(0)
  })

  it('starts with provided initial value', () => {
    const { count } = useCounter(5)
    expect(count.value).toBe(5)
  })

  it('increments correctly', () => {
    const { count, increment } = useCounter()
    increment()
    increment()
    expect(count.value).toBe(2)
  })

  it('resets to initial value', () => {
    const { count, increment, reset } = useCounter(3)
    increment()
    reset()
    expect(count.value).toBe(3)
  })
})
```

---

## 常見陷阱

1. **`async` 觸發事件後要 `await`**：Vue 事件觸發是非同步更新 DOM，需 `await button.trigger('click')` 後才能斷言 DOM 變化

2. **TypeScript 嚴格模式在測試中仍有效**：測試檔也受 `noUnusedLocals` 等規則限制，宣告後未使用的變數會讓建置失敗

3. **`public/` 資源在 jsdom 環境不可用**：`/icons.svg` 不會真的被載入，若測試涉及 SVG sprite 需 mock 或忽略

4. **Tailwind class 在 jsdom 無法驗證樣式**：jsdom 不處理 CSS，`wrapper.find('.text-red-500').classes()` 可以，但 `getComputedStyle()` 的值不可靠

5. **`mount` vs `shallowMount`**：`mount` 完整渲染子元件，`shallowMount` 將子元件 stub 掉；對小型元件用 `mount`，測試大型元件時若只關心本層邏輯用 `shallowMount`

---

## 執行測試（設定後）

```bash
npm run test        # 監聽模式，檔案變更時自動重跑
npm run test:run    # 單次執行（CI 環境用）
```

覆蓋率報告（需額外設定 `@vitest/coverage-v8`）：

```bash
npx vitest run --coverage
```
