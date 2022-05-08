---
title: Vue.js 3 - Slot
date: 2022-05-07 19:41:23
tags: ["Vue","slot"]
---

![](https://i.imgur.com/NBBY8vo.png)

剛學習 vue 的時候，對 slot 完全一竅不通，不了解要如何挖空多元件，來放入資料，更是不知道他能使用的時機是什麼時候，種種因素，讓我從官方文件找些範例釐清，也找了些文章閱讀加深記憶，順手也紀錄在部落格上～

使用時機：
- 使用同一個 元件 , 但只有內文不同。
- 有時候資料是靜態，不常變動，甚至需要大量重複。這情況不必要使用 props。


1. 共用樣式
從 [官網的範例](https://sfc.vuejs.org/#eyJBcHAudnVlIjoiPHNjcmlwdD5cbmltcG9ydCBGYW5jeUJ1dHRvbiBmcm9tICcuL0ZhbmN5QnV0dG9uLnZ1ZSdcbiAgXG5leHBvcnQgZGVmYXVsdCB7XG4gIGNvbXBvbmVudHM6IHsgRmFuY3lCdXR0b24gfVxufVxuPC9zY3JpcHQ+XG5cbjx0ZW1wbGF0ZT5cbiAgPEZhbmN5QnV0dG9uPlxuICAgIENsaWNrIG1lIDwhLS0gc2xvdCBjb250ZW50IC0tPlxuIFx0PC9GYW5jeUJ1dHRvbj5cbjwvdGVtcGxhdGU+IiwiaW1wb3J0LW1hcC5qc29uIjoie1xuICBcImltcG9ydHNcIjoge1xuICAgIFwidnVlXCI6IFwiaHR0cHM6Ly9zZmMudnVlanMub3JnL3Z1ZS5ydW50aW1lLmVzbS1icm93c2VyLmpzXCJcbiAgfVxufSIsIkZhbmN5QnV0dG9uLnZ1ZSI6Ijx0ZW1wbGF0ZT5cbiAgPGJ1dHRvbiBjbGFzcz1cImZhbmN5LWJ0blwiPlxuICBcdDxzbG90Lz4gPCEtLSBzbG90IG91dGxldCAtLT5cblx0PC9idXR0b24+XG48L3RlbXBsYXRlPlxuXG48c3R5bGU+XG4uZmFuY3ktYnRuIHtcbiAgY29sb3I6ICNmZmY7XG4gIGJhY2tncm91bmQ6IGxpbmVhci1ncmFkaWVudCgzMTVkZWcsICM0MmQzOTIgMjUlLCAjNjQ3ZWZmKTtcbiAgYm9yZGVyOiBub25lO1xuICBwYWRkaW5nOiA1cHggMTBweDtcbiAgbWFyZ2luOiA1cHg7XG4gIGJvcmRlci1yYWRpdXM6IDhweDtcbiAgY3Vyc29yOiBwb2ludGVyO1xufVxuPC9zdHlsZT4ifQ==)可以看到，讓按鈕的文字可以彈性的變化。

2. named slot(具名插槽) 
* 建立一個元件，其中含有多個slot區塊
[範例連結](https://replit.com/@as45986/Named-Slots#src/App.vue)

3. Scoped slots（作用域插槽）
* 把子元件的資料取出來，給父層使用
* 不同於前面 slot 的使用，是由父層輸入資料呈現於頁面
[範例](https://replit.com/@as45986/Scoped-Slots#src/App.vue)

資料來源:
[vuejs](https://vuejs.org/)
[不只懂 Vue 語法：什麼是 slot？請示範 slot 的用法？](https://ithelp.ithome.com.tw/articles/10273298?sc=iThomeR)
[用範例理解 Vue.js #18：Slot](https://ithelp.ithome.com.tw/articles/10194384)
