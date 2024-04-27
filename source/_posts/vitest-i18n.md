---
title: vitest-i18n
date: 2024-04-27 13:12:36
tags: ["Test"]
categories: Test
---

最近嘗試在專案內加入測試，詢問朋友後建議先從 utils 中共用的邏輯函式開始，而在共用的邏輯函式中，會有幾個是 i18n 相關的函式。
因為頁面中常會用到將數值，轉換為顯示在畫面上的文字，如下：

```TypeScript
const getType = (type: number) => {
    switch (type) {
        case 0:
            return t('Type.Auto')
        case 1:
            return t('Type.Manual')
        case 2:
            return t('Type.Mix')
        default:
            return null
    }
}
```

在進行測試時，總想說是否要先選定一語系，如中文\英文，然後確認他轉換的是否為該文字
但是經過嘗試後，覺得或許不應該是檢查轉換後的文字，應該要以對應 key 來檢查。
參考 [overflow](https://stackoverflow.com/questions/72260793/how-to-mock-stub-vue-i18n)

- 因此專案是使用 quasar，所以在 i18n.t 方法，引入的位置是 boot/i18n.ts
- 在測試中，可以執行測試函式，帶入對應數值，並檢查是否回傳對應的 key

```TypeScript

// 在測試中覆寫模擬 i18n.t 方法
vi.mock('../../../src/boot/i18n', () => ({
    i18n: {
        global: {
            t: (key: string) => key, // 提供模擬的 t 函式
            locale: 'en' // 假設默認的 locale 值
        }
    }
}))

describe('getType', () => {
    it('should return the correct string for type 0', () => {
        expect(getTradingType(0)).toBe('Type.Auto')
    })

    it('should return the correct string for type 1', () => {
        expect(getTradingType(1)).toBe('Type.Manual')
    })

    it('should return the correct string for type 2', () => {
        expect(getTradingType(2)).toBe('Type.Mix')
    })
    it('should return null for an unknown type', () => {
        expect(getTradingType(999)).toBeNull()
    })
})
```

### 小結上述方式：

使用全局模擬，vi.mock 全局模擬 i18n.t 方法，並提供模擬的 t 函式，這樣就可以在測試中，直接檢查是否回傳對應的 key，而不用檢查轉換後的文字。

另外，在跨頁面也會有共用的下拉選單，將此下拉選單的選項，也提取出來，並進行測試。

```TypeScript

const directionOpt = computed(() => ([
    { label: t('Shared.right'), value: 0 },
    { label: t('Shared.wrong'), value: 1 }
]))

```

原本也想是否直接檢查 key 是否正確即可。但基於困惑，就嘗試丟 chatGpt 詢問

<!-- more -->

- 給予 mockT 方法，並提供對應的 key，並檢查是否有被呼叫
- 定義 directionOpt，並檢查是否回傳對應的 key
- 但此方式卻 directionOpt 撰寫於測試檔案中

```TypeScript
const mockT = vi.fn((key) => {
    const translations = {
        'Shared.right': '正確',
        'Shared.wrong': '錯誤'
    }
    return translations[key]
})
// 定義 directionOpt
const directionOpt = computed(() => ([
    { label: mockT('Shared.right'), value: 0 },
    { label: mockT('Shared.wrong'), value: 1 }
]))
describe('directionOpt', () => {
    it('should return the correct options', () => {

        const options = directionOpt.value

        expect(options).toEqual([
            { label: '正確', value: 0 },
            { label: '錯誤', value: 1 }
        ])

        // 确保模拟的 t 函数被正确调用
        console.log('mock', mockT)
        expect(mockT).toHaveBeenCalledWith('Shared.right')
        expect(mockT).toHaveBeenCalledWith('Shared.wrong')
    })
})
```

後來，我回歸我想測試的目標在於，原函式 computed 是否使用這些 key，因此，就以以下方式進行測試

```typescript
describe("directionOpt", () => {
  it("should return the correct options", () => {
    const options = directionOpt.value;

    // t 函式直接反回 key 值，驗證 computed 是否使用这些 key
    expect(options).toEqual([
      { label: "Shared.right", value: 0 },
      { label: "Shared.wrong", value: 1 },
    ]);
  });
});
```
