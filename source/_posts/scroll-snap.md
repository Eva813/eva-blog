---
title: scroll snap
date: 2024-10-30 07:38:54
tags: ['css']
---

CSS Scroll Snap 是一種用於控制滾動行為的 CSS 模組，允許開發者在使用者停止滾動時，自動將視圖對齊到特定的元素。這種功能常用於創造更流暢的用戶體驗，例如分頁文章、圖片輪播等。

## 主要由兩個屬性來控制：

scroll-snap-type：設置在滾動容器（父元素）上，用於定義滾動軸向和滾動對齊的行為。

格式為：scroll-snap-type: [軸向] [行為]
軸向可以是 x（水平滾動）、y（垂直滾動）或 both（同時支持水平與垂直）。
行為可以是 mandatory 或 proximity：
mandatory：滾動必須對齊到指定位置，確保每次停下來都會對齊。
proximity：當滾動接近某個指定的位置時，會進行對齊。
例如：

```
.scroll-container {
  scroll-snap-type: y mandatory; /* 在垂直方向強制對齊 */
}
```
scroll-snap-align：設置在滾動內容項目（子元素）上，定義這些元素在滾動容器中的對齊方式。

可選的值有 start、center、end，決定項目在滾動結束時如何與容器對齊。
例如：

```css
.scroll-item {
  scroll-snap-align: start; /* 將項目對齊到滾動容器的頂端 */
}
```

| 屬性               | 描述                                                                 |
|--------------------|----------------------------------------------------------------------|
| `scroll-snap-type` | 定義滾動容器的捕捉行為，包括方向（x, y, both）和強制程度（mandatory, proximity）。 |
| `scroll-padding`   | 設置容器內邊距，影響捕捉點的位置。                                   |
| `scroll-snap-align`| 定義子元素在容器中的對齊方式（start, center, end）。                 |
| `scroll-snap-stop` | 控制是否在特定元素上停留，默認情況下僅在停止滾動時觸發。 |


## 使用時注意事項

1. 跨瀏覽器不一致性
雖然大多數現代瀏覽器都支持 Scroll Snap，但在行為上可能會有一些細微的差異。請務必在各種瀏覽器上測試你的實現，確保用戶能夠獲得一致的體驗。像是使用 [cna I us](https://caniuse.com/) 確認支援。
對於某些舊版本仍然需要前綴。使用 WebKit 前綴來支持較舊的 Safari 和 Chrome。

```css
.scroll-container {
  -webkit-scroll-snap-type: y mandatory; /* 用於舊版 Safari 和 Chrome */
  scroll-snap-type: y mandatory; /* 標準屬性 */
}
```

使用 @supports 進行功能檢測：你可以使用 @supports 檢測瀏覽器是否支持 Scroll Snap，並提供相應的回退方案。例如，如果某些瀏覽器不支持，則可以使用 JavaScript 實現類似的行為。

```css

@supports (scroll-snap-type: y mandatory) {
  .scroll-container {
    scroll-snap-type: y mandatory;
  }
}

@supports not (scroll-snap-type: y mandatory) {
  .scroll-container {
    /* 恢復樣式，或提供 JavaScript 實現滾動行為 */
  }
}
```

2. 預期外的滾動行為
如果發現未對齊正確，或者容器的尺寸設置不當，可能會出現預期外的滾動行為。
解決方案：
設置固定尺寸：確保滾動容器和子元素的尺寸設置正確。對於橫向滾動，應確保每個項目的寬度是明確的，對於縱向滾動，應設置每個項目的高度。例如：

```css
複製程式碼
.scroll-container {
  overflow-x: scroll; /* 必須設置 overflow 屬性 */
  scroll-snap-type: x mandatory; /* 水平捕捉點 */
}

.scroll-item {
  width: 100%; /* 保證每個項目都佔據滾動區域 */
  scroll-snap-align: start; /* scroll-snap-align 決定了子元素在滾動容器中如何對齊 */
}
```



3. 性能影響

當頁面內容繁多，或包含大量動畫和捕捉點時，可能會導致性能問題。這會導致滾動卡頓，特別是在移動設備上。

解決方案：
避免過度使用捕捉點：不要對每一個小元素都設置 scroll-snap-align，僅在核心內容（如頁面段落、關鍵節點）設置捕捉點。例如，圖片輪播或長段落之間可以有捕捉點，但不需要對每個小項目設置。

除此之外，可以嘗試 lazy load：對於圖片或大型內容，使用懶加載技術（如 Intersection Observer API）可以避免一次性渲染過多內容，從而減輕滾動過程中的性能負擔。

- 使用 Intersection Observer 實現懶加載: 用來觀察（observe）當指定元素接觸到父層以上或者是視窗的方法。

```javaScript
const images = document.querySelectorAll('.lazy-load');
const observer = new IntersectionObserver(entries => {
  entries.forEach(entry => {
    if (entry.isIntersecting) {
      const img = entry.target;
      img.src = img.dataset.src; // 替換為真正的圖片
      observer.unobserve(img); // 停止觀察
    }
  });
});

images.forEach(image => {
  observer.observe(image); // 觀察每個懶加載圖片
});

```

- 減少不必要的動畫：過多的動畫和滾動事件監聽器會降低滾動性能，特別是如果動畫效果頻繁更新。例如，可以使用 requestAnimationFrame 來控制滾動動畫的頻率，避免過多的重排和重繪。或是嘗試，節流滾動事件：

```javascript
let isScrolling;
window.addEventListener('scroll', () => {
  window.clearTimeout(isScrolling);
  isScrolling = setTimeout(() => {
    // 在滾動結束後觸發的操作
    console.log('滾動結束');
  }, 66); // 節流時間間隔
});

```

參考文章：
[超好用的 Web API - Intersection Observer](https://jim1105.coderbridge.io/2022/07/30/intersection-observer/)
[No JS required — you can do this with CSS!](https://medium.com/@bogdanfromkyiv/no-js-required-you-can-do-this-with-css-e4635e40502c)