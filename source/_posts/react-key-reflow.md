---
title: React 重置技巧：React Key 與 CSS offsetWidth
date: 2025-04-16 20:03:42
tags: [side-project]
categories: side-project
---


在前端開發中，動畫與互動是提升用戶體驗的關鍵元素。然而，當動畫與元件狀態、生命周期交織在一起時，實作上就可能遇到各種挑戰。

這篇文章記錄我在一個 React 專案中，解決「動畫重複觸發與重置」問題的過程，深入比較了使用 key 重置與 CSS Reflow（offsetWidth） 重置的方式


## 問題概述

在這個專案中，我有兩個自訂的元件，各自內部包含一個相同結構的 ``<input>``。當使用者點擊某個操作後，我希望這些 input：

<!-- more -->

- 執行一次 CSS 動畫（例如 fade-in）
- 自動獲得焦點，以利立即輸入內容

實務操作時卻發現動畫無法正常重新觸發。透過分析發現：

- 由於兩個元件 input 結構相同，React 為效能考量，複用了 DOM。
- 導致 CSS 動畫無法重新觸發，即便強制 re-render 也不一定能順利重新播放。

這時我開始思考：有沒有方法可以「重置動畫」，但不要整個元件都重建？

我開始尋找「能重置動畫但不需重建元件」的方法，最終發現兩個可行方案。


### 方法一：使用 key 重建元件
在 React 中，每個元件都可以透過 key 來標示其唯一性。當 key 改變時，React 會：

卸載原本的元件

掛載一個全新的元件實例

這表示：元件會完全重置，包括其 state、effects 和動畫效果。


- **優點**：
  - 完整重置狀態與動畫
  - 純 React 機制，簡單易懂
- **缺點**：
  - 整個元件卸載重建，效能成本高
  - 所有 effect 重跑一遍，需注意副作用

範例程式碼：
```jsx

import React, { useState } from 'react';

const App = () => {
  const [keyIndex, setKeyIndex] = useState(0);

  const handleReset = () => setKeyIndex(prev => prev + 1);

  return (
    <div>
      <button onClick={handleReset}>重置元件</button>
      <InputBox key={keyIndex} />
    </div>
  );
};

const InputBox = () => {
  return (
    <input
      className="fade-in"
      autoFocus
      placeholder="輸入一些內容..."
    />
  );
};
```

- 優點：
可徹底重置所有狀態與動畫

不需額外操作 DOM，純粹靠 React 的 key 機制

- 缺點：
整個元件會被卸載與重建，效能開銷較大

所有副作用與初始化邏輯（如 useEffect）都會重新執行



### 方法二：使用 offsetWidth 強制 CSS 重排（Reflow）

如果只需要「重播動畫」而不需要重置整個元件狀態，那其實 不必重建 DOM 元素，我們可以用 CSS 動畫與 DOM 操作來達成。

原理是透過：

移除動畫類別

存取 offsetWidth 觸發 Reflow

重新加入動畫類別

這樣就能「騙過」瀏覽器認為動畫是全新的一次播放。

- **優點**：
  - 輕量級 DOM 操作
  - 適合頻繁動畫觸發
- **缺點**：
  - 需透過 DOM 操作或 state 管理 class
  - 無法重置元件內部狀態


```js

const element = document.getElementById("inputElement");

element.classList.remove("animate");
void element.offsetWidth;
element.classList.add("animate");
```

React 實作（使用 state）：

```jsx

import React, { useState } from 'react';

const InputWithAnimation = () => {
  const [animate, setAnimate] = useState(true);

  const resetAnimation = () => {
    setAnimate(false);
    setTimeout(() => setAnimate(true), 10);
  };

  return (
    <div>
      <button onClick={resetAnimation}>重新播放動畫</button>
      <input
        className={animate ? "fade-in" : ""}
        autoFocus
        placeholder="輸入文字..."
      />
    </div>
  );
};
```

- 優點：
只更新 DOM 屬性，不會造成元件重建

效能負擔小，適合單一或少量元素的動畫重播

- 缺點：
需要操作 DOM（透過 ref 或 state 控制 class）

只針對動畫，無法重置元件內部狀態


### 最後選擇的方式：offsetWidth 重排，更高效又穩定

在衡量了需求與效能之後，我最終選擇使用 CSS offsetWidth 的方式來重播動畫，原因如下：

#### 技術考量：
我只需要動畫重置與 focus 效果，不需要整個元件重建

若使用 key，元件會被卸載重建，對效能影響太大

CSS 方法可以更精準控制動畫行為

#### 效能層面：
使用 offsetWidth 只會觸發局部重排

不會進入 React 的 Virtual DOM Diff 過程

更適合需要高互動性與動畫頻繁觸發的場景

#### 技術總結與實務建議

建議原則：
動畫重播：使用 offsetWidth + class 操控，效能較佳且針對性強
狀態重置：使用 key 強制重新掛載，簡單直接但效能成本較高




---

參考
[How to make your app faster with React's key prop](https://dev.to/olivare/how-to-make-your-app-faster-with-reacts-key-prop-170n)

[What purpose does void element.offsetWidth serve?](https://stackoverflow.com/questions/60686489/what-purpose-does-void-element-offsetwidth-serve/69029892#69029892)

[Mastering React Re-Renders : The Key Prop Hack You Need to Know](https://dev.to/malapashish/mastering-react-re-renders-the-key-prop-hack-you-need-to-know-17hh)
