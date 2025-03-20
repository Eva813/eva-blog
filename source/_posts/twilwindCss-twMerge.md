---
title: twilwindCss-twMerge
date: 2025-03-20 19:43:16
tags: [Tailwind CSS]
categories: frontend
---

在開發 React 或 Vue 應用時，動態組合 Tailwind CSS class 是常見的需求。但當 class 來源多樣（例如 props 傳入、條件渲染等），你可能會遇到 class 重複、衝突或難以維護的問題，這時候，twMerge 就能幫助你解決這些困難。

## 什麼是 twMerge

`twMerge` 是一個專為 **Tailwind CSS** 設計的 class 合併工

- **衝突解決**：當你從不同來源（例如預設樣式和 props 傳入的 class）合併 class 時，可能會出現相同屬性的重複定義，twMerge 會自動解決這些衝突，保證最終只保留正確的 class。
- **動態組合**：在實際應用中，很多時候 class 字符串是根據條件組合出來的，直接拼接可能會導致意料之外的覆蓋。twMerge 能夠讓你的 class 合併更有條理，避免混淆。

安裝方式：

```bash

npm install tailwind-merge
# 或
yarn add tailwind-merge
```
基本使用範例：

```jsx

import { twMerge } from 'tailwind-merge';

const classes = twMerge("p-4", "p-2");
console.log(classes); // 輸出: "p-2"（自動移除衝突的 p-4）

```
<!-- more -->

## 為什麼不用手動拼接 class？

### **無法處理 Tailwind class 衝突**

當兩個 class 有衝突時，手動拼接無法確保後來的 class 是否正確覆蓋前者。例如：

```jsx

const finalClasses = `p-4 p-2`;
console.log(finalClasses); // "p-4 p-2" （無法移除衝突 class）

```
### **條件渲染時 class 可能重複或失效**

```jsx

const isActive = true;
const finalClasses = `px-4 py-2 ${isActive ? "bg-blue-500" : "bg-gray-200"} bg-red-500`;
console.log(finalClasses);
// "px-4 py-2 bg-blue-500 bg-gray-200 bg-red-500" （背景顏色衝突）

// 改使用 twMerge
const finalClasses = twMerge("px-4 py-2", isActive ? "bg-blue-500" : "bg-gray-200", "bg-red-500");
console.log(finalClasses);
// "px-4 py-2 bg-red-500" （自動解決衝突）

```

### **也有使用模板字串（template literal）來動態拼接 class，例如：**

```jsx

const finalClasses = `px-4 py-2 bg-blue-500 text-white ${isDisabled ? "opacity-50 cursor-not-allowed" : ""}`;

```

雖然，這樣可以根據條件動態組合 class，不過要注意兩點：

1. **Tailwind CSS 掃描限制**
    若 class 是在字串插值中產生的，Tailwind 的掃描器可能無法檢測到某些動態組合的 class，這會導致對應 CSS 未被生成。這時候可能需要在 Tailwind 設定檔中加入 safelist。
    
2. **衝突管理**
    當有多個來源的 class 存在衝突（例如兩個不同的背景色），你必須手動確保拼接順序正確。如果衝突較多，使用 twMerge 可以更方便地自動解決這些衝突。

### twMerge 的好處



| 問題          | 手動拼 class                                      |使用 twMerge           |
|----------------|--------------------------------------------------|--------------------  |
| 衝突解決      | 必須自己追蹤哪個 class 最後生效                       | 自動保留後出現的 class，刪除重複或衝突項 [wsquare.io](https://www.wsquare.io/blogs/how-to-use-and-get-benefits-from-tailwind-merge)
| 動態組合      | 條件判斷時拼接字串易漏寫或重複                           | 傳多個參數給 twMerge，即使 class 來源複雜也只回傳一組乾淨結果 [tailkits.com](https://tailkits.com/blog/tailwind-css-class-conflicts-using-tailwind-merge/)|
| 維護成本      | 隨著專案變大，class 字串容易失控                        | twMerge 會緩存結果，減少重複運算並讓程式碼更易讀 |


## twMerge 的實踐
### **在 React 中條件渲染 Tailwind class**

```jsx

const Button = ({ isDisabled }) => {
  return (
    <button className={twMerge(
      "px-4 py-2 rounded",
      isDisabled && "opacity-50 cursor-not-allowed"
    )}>
      Click Me
    </button>
  );
};

```

當 `isDisabled = true`，twMerge 會**自動移除 `false` 的 class**，結果變成：

```jsx

// isDisabled = true 時
"px-4 py-2 rounded opacity-50 cursor-not-allowed"

// isDisabled = false 時
"px-4 py-2 rounded"

```

這樣可以 **避免`undefined` 或 `false` 的 class 影響最終輸出**。

---

### **組件化時合併 props 傳入的 class**

當你開發 **可重用 UI 元件** 時，通常會允許使用者傳入 `className` 來覆蓋預設樣式：

```jsx

const Text = ({ children, className }) => {
  return (
    <div className={twMerge("font-normal text-lg", className)}>
      {children}
    </div>
  );
};

```

使用方式：

```jsx
<Text className="font-bold text-red-500">Hello</Text>

```

這樣 `twMerge` 會確保：

- `font-bold` 正確覆蓋 `font-normal`
- `text-red-500` 正確覆蓋 `text-lg`

## 結論
在開發 **需要動態組合類別的元件**，或 **允許使用者自訂樣式覆蓋** 的情境下，`twMerge` 是一個非常實用的工具。它能夠幫助你：  

**自動解決 class 衝突**  
不必手動追蹤哪些類別可能互相覆蓋，`twMerge` 會確保最後宣告的樣式生效（例如 `p-4 p-2` 最終保留 `p-2`）。  

**支援條件渲染**  
優雅地處理條件式樣式，避免 `false`、`undefined` 影響最終 class，讓程式碼更乾淨。  

**提升程式碼可讀性與維護性**  
讓動態組合多個來源的 class 變得簡單清晰，減少維護成本，提高開發效率。

[Override Tailwind Classes using Tailwind-Merge](https://antematter.io/blogs/mastering-css-customization-with-tailwind-merge)