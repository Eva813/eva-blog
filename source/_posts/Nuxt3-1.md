---
title: Nuxt3-取得資料的方式
date: 2021-12-10 20:08:43
tags: ["Nuxt"]
---

![](https://i.imgur.com/FAgr4vk.png)

## useAsyncData

```javascript
const {
  data: Ref<DataT>,
  pending: Ref<boolean>,
  refresh: (force?: boolean) => Promise<void>,
  error?: any
} = useAsyncData(
  key: string,
  fn: () => Object,
  options?: { lazy: boolean, server: boolean }
)
```
* 頁面、組件中都可以使用useAsyncData 取得資料
* Key ：寫一個唯一的id
  請求資料的函式
* 使用 useAsyncData 會回傳物件
  data : 返回的資料 
  pedding : 指出資料是否被取得
  refresh : 是否要刷新資料
### Option 介紹
* Lazy 延遲載入，直到數據獲取時，才顯示於頁面
* 默認會是 false
* Server 頁面是在server端要載入，就可以設定為true
* Pick 從回傳的資料中，選擇要使用的key值

### useAsyncData撰寫方式的範例
* 本地建立資料夾
  * server/api/count.ts

```javascript
let counter = 0
export default () => {
  counter++
  return JSON.stringify(counter)
}
```
* `app.vue`，使用 useAsyncData

```javascript
<script setup>
const { data } = await useAsyncData('count', () => $fetch('/api/count'))
</script>
<template>
  Page visits: {{ data }}
</template>

```

## useFetch
* 頁面、組件中可以使用useFetch獲取任意URL資源
* useFetch包裝 了useAsyncData 和 $fetch
* 會根據 URL 自動生成 Key，也會去推斷API 的類型a
```javascript
const {
  data: Ref<DataT>,
  pending: Ref<boolean>,
  refresh: (force?: boolean) => Promise<void>,
  error?: any
} = useFetch(url: string, options?)
```

```javascript
<script setup>
const { data } = await useFetch('/api/count')
</script>

<template>
  Page visits: {{ data.count }}
</template>
```

## 補充：其他2個類似方式
* useLazyAsyncData : 方法等於useAsyncData，僅設置了lazy選項為true
* useLazyFetch : 方法等於useFetch，僅設置了lazy選項為true

## 實作範例：
* `/api/mountains/everest`
  * API 會產生的物件資料

```javascript
{
  "title": "Mount Everest",
  "description": "Mount Everest is Earth's highest mountain above sea level, located in the Mahalangur Himal sub-range of the Himalayas. The China–Nepal border runs across its summit point",
  "height": "8,848 m",
  "countries": [
    "China",
    "Nepal"
  ],
  "continent": "Asia",
  "image": "https://upload.wikimedia.org/wikipedia/commons/thumb/f/f6/Everest_kalapatthar.jpg/600px-Everest_kalapatthar.jpg"
}
```
Nuxt 3 為了提高效率會在取得資料後，緩存在當前頁面的payload
官方建議：如果只打算在組件中使用到 title 和 description ，你可以使用關鍵的屬性運用即可
選項中加入 pick，裡面的值就是取得資料中的key值

```javascript
<script setup>
const { data: mountain } = await useFetch('/api/mountains/everest', { pick: ['title', 'description'] })
</script>

<template>
  <h1>{{ mountain.title }}</h1>
  <p>{{ mountain.description }}</p>
</template>
```

[參考資料](https://v3.nuxtjs.org/docs/usage/data-fetching)
