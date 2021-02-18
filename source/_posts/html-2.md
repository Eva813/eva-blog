---
title: HTML基本介紹-2
date: 2021-02-18 14:12:02
tags: HTML
---

# HTML 元素 (Element)
```html
 <p>This is paragraph.</p>
```

* 完整的標籤會有開始、結尾：`<p>`,`</p>`:分別為opening tag以及closing tag
* 標籤中間包圍的This is paragraph.是內容
* 起始標籤 + 內容 + 結束標籤:表示一個 HTML元素。

# HTML 標籤屬性 (Attribute)
* `<p class="myP">This is paragraph.</p>`
* 有些Attribute是HTML內建，如：<a href="./first.html">
* 有些是為了用於css或是javascript的部分所加上去。如：class與id的使用。

###  `class`與`id`
是在於 ID 選擇器在一個 HTML 文件中只能被使用一次，而 Class 選擇器在一個 HTML 文件中可以被使用多次;ID 選擇器可以被 Javascript 中的 GetElementByID 函數所運用，而 Class 選擇器無法被 Javascript 運用到。
