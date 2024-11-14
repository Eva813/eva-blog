---
title: css-grid
date: 2024-01-06 12:15:17
tags: ["CSS"]
---

在 Youtube 看到 Kevin Powell 介紹使用 grid 來作為 wrapper，以自適應網頁的縮放。

首先基本常用 grid 方式

- 創建一個包含三欄等寬的網格布局

```css
.content-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}
```

加入使用 using named grid lines，定義出欄的 content 範圍

```css
.content-grid {
  display: grid;
  grid-template-columns: 1fr [content-start] 1fr [content-end] 1fr;
}
```

設計一個 class 給他，如果在 content-grid 以下的元素，會將以下的元素放在 content 之間

- 應用到 `.content-grid` 內部的每一個直接子元素

```css
.content-grid {
  display: grid;
  grid-template-columns: 1fr [content-start] 1fr [content- end] 1fr;
}

.content-grid > * {
  grid-column: content;
}
```

### Layout grid 可以先設置：

- 就可以看到 content 範圍
  ![image](https://hackmd.io/_uploads/HyCjULL_p.png)
  ![image](https://hackmd.io/_uploads/HkJWv8UO6.png)

<!-- more -->

## 設置 breakout

```css
.content-grid {
  display: grid;
  grid-template-columns: 100px [breakout-start] 100px [content-start] 1fr [content-end] 100px [breakout-end] 100px;
}

.content-grid > * {
  grid-column: content;
}

.content-grid > .breakout {
  grid-column: breakout;
}
```

![image](https://hackmd.io/_uploads/HycvDLIO6.png)

## 不可缺的 full-width

- 利用網格線的 最左側以及最右側 grid line

```css
.content-grid {
  display: grid;
  grid-template-columns: [full-width-start] 100px [breakout-start] 100px [content-start] 1fr [content-end] 100px [breakout-end] 100px [full-width-end];
}

.content-grid > * {
  grid-column: content;
}

.content-grid > .breakout {
  grid-column: breakout;
}

.content-grid > .full-width {
  grid-column: full-width;
}
```

- 希望區塊的背景可以 full-with 但內容符合文章(content)寬度
  `.content-grid > .full-width`

```css
.content-grid {
  display: grid;

  grid-template-columns: [full-width-start] 100px [breakout-start] 100px [content-start] 1fr [content-end] 100px [breakout-end] 100px [full-width-end];
}

.content-grid > :not(.breakout, .full-width),
.full-width > :not(.breakout, .full-width) {
  grid-column: content;
}

.content-grid > .breakout {
  grid-column: breakout;
}

.content-grid > .full-width {
  grid-column: full-width;
  display: grid;
  grid-template-columns: inherit;
}
```

## 更細微的調整，設置變數以符合 responsive

```css
.content-grid{
	 --padding-inline: 2rem ;
	 --content-max-width: 70ch;
	 --breakout-max-width: 85ch
	--breakout-size: calc(
(var(--breakout-max-width) - var(--content-max-width)) / 2
);
	display: grid;

	grid-template-columns:
	[full-width-start] minmax(var(--padding-inline), 1fr)
	[breakout-start] minmax(0, var(--breakout-size))
	[content-start] min(100% - (var(--padding-inline) * 2 ), var(--content-max-width)) [content-end] minmax(0, var(--breakout-size)) [breakout-end]
	minmax(var(--padding-inline), 1fr) [full-width-end];
}
```

1. --padding-inline : 設計整體頁面 padding
2. ch：CSS3 加入了 ch 這個單位，廣義來說就是『一個字元』，相當於用來呈現的字體裡『0』這個字形的步進度量 (advance measure)

- 這裡，70ch 意味著最大寬度是當前字體「0」字符寬度的 70 倍。

3. breakout-size: 計算為 breakout 的最大寬與 content 最大寬度 之差的一半，用來設定邊界
4. 整體 grid-template-columns 的設計

- full-width-start 網格的起始
- minmax(var(--padding-inline), 1fr) : 設置到 full-width 最大大小為 1fr, 最小則會保有 padding-inline 間距
- [breakout-start] 是下一個網格線的名稱: 大小設置為從 0 到 var(--breakout-size)
- `[content-start] min(100% - (var(--padding-inline) * 2 ), var(--content-max-width)) [content-end]`: 標記了內容區域的開始和結束，--content-max-width 是 70ch , 如果寬度小於 70ch ，就會使用`100% - (var(--padding-inline) * 2 `, 如果大於 70ch , 就會使用 content-max-width

5. 在 content 後，接續 breakout-end, full-width-end 同樣相對 start 的區間

### 小結

在未來可以嘗試應用此 grid template 作為整體網頁 layout 設計，且套用方式可以修改以下變數即可彈性變動整體的寬度以及斷點。

```
--padding-inline: 2rem;
--content-max-width: 70ch;
--breakout-max-width: 85ch;
```

### 補充介紹 `min()`, `minmax()`

`minmax()`
minmax() 函數在 CSS Grid 布局中常用來定義網格軌道（列或行）的大小範圍。這個函數接受兩個參數：最小值和最大值，用來設置一個軌道的最小和最大大小。當網格容器調整大小時，這個軌道的實際大小會在這個範圍內變化。

例如：

```css
grid-template-columns: minmax(100px, 1fr);
```

這裡，該列的最小寬度設為 100 像素，最大寬度設為 1fr（即可用空間的一個分數單位）。如果容器的寬度大於 100 像素，該列將擴展以佔用更多可用空間，但不會小於 100 像素。

`min()`
min() 函數在 CSS 中用於選擇一組值中的最小值。在網格佈局中，它可以用來確保某個軌道的大小不會超過一系列可能值中的最小值。

例如：

```css
grid-template-columns: min(100%, 500px);
```

這裡，該列的寬度將被設置為 100% 和 500 像素中的較小值。這意味著，如果容器的寬度超過 500 像素，該列的寬度將被限制在 500 像素；如果容器的寬度小於 500 像素，則該列的寬度將等於容器的寬度。

[MDN-Grid layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_grid_layout/Grid_layout_using_named_grid_lines)
