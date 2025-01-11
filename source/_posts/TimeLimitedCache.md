---
title: 刷題練習 - Cache with Time Limit
date: 2025-01-04 19:53:52
tags: ["Leetcode"]
categories: Leetcode
---

Write a class that allows getting and setting key-value pairs, however a time until expiration is associated with each key.

The class has three public methods:

`set(key, value, duration)`: accepts an integer `key`, an integer `value`, and a `duration` in milliseconds. Once the `duration` has elapsed, the key should be inaccessible. The method should return true if the same un-expired key already exists and false otherwise. Both the value and duration should be overwritten if the key already exists.

`get(key)`: if an un-expired key exists, it should return the associated value. Otherwise it should return -1.

`count()`: returns the count of un-expired keys.

## 中文說明
`set(key, value, duration)`： 接受一個整數的 key、一個整數值 value，以及一個毫秒為單位的時間 duration。當持續時間結束後，這個鍵值對將不能被存取。如果相同且未過期的鍵存在，則回傳 true，若不存在則回傳 false。如果這個鍵已經存在，其值與持續時間都應該被新傳入的 value 與 duration 覆寫。
`get(key)`：如果存在未過期的鍵，回傳其對應的值。若沒有相對應的鍵，則回傳 -1。
`count()`：回傳現存未過期的鍵的總數量。

<!-- more -->
## 解題思路：
1.理解題目需求：
  要做一個可以「設定時間限制」的快取（cache）。
  支援三個方法：
  set(key, value, duration)：設定一個緩存，並在 duration 毫秒後自動移除。
  get(key)：獲取緩存的值，若該緩存過期則返回 -1。
  count()：返回當前未過期的緩存數量。
2.使用 Map ，查找 (get) 和新增 (set) 都很快。
  - 可以存複雜的結構（像我們要存值和計時器 ID）


## 解題

```javaScript

var TimeLimitedCache = function() {
    this.cache = new Map()
};

/** 
 * @param {number} key
 * @param {number} value
 * @param {number} duration time until expiration in ms
 * @return {boolean} if un-expired key already existed
 */
TimeLimitedCache.prototype.set = function(key, value, duration) {
    // 先確認 key 是否存在
    const found = this.cache.has(key);
    // 如果鍵已存在（未過期），清除舊的計時器，避免舊計時器的干擾
    if(found){
        clearTimeout(this.cache.get(key).timerId);
    }
    // 無論是否已有 key，都會設定新的值和計時器：
    this.cache.set(key,{
        value,
        timerId: setTimeout(()=> this.cache.delete(key),duration)
    })
    // 此函式要回傳 boolean, if the same un-expired key already exists(true)
    return found
};

/** 
 * @param {number} key
 * @return {number} value associated with key
 */
TimeLimitedCache.prototype.get = function(key) {
    if(this.cache.has(key)){
        return this.cache.get(key).value;
    }else{
        return -1;
    }
};

/** 
 * @return {number} count of non-expired keys
 */
TimeLimitedCache.prototype.count = function() {
    return this.cache.size;
};
```

### 補充說明
1. 為什麼存在(found)時要 clearTimeout？
假設緩存中已有鍵 key，且其倒計時尚未結束：
- 如果直接覆蓋而不清除舊的 setTimeout 計時器，舊計時器仍然會在原定的時間觸發刪除操作。
- 清除舊的計時器 (clearTimeout) 可以避免多餘的操作，確保新的計時器能正確控制過期時間。

2. setTimeout 的參數作用是什麼？

```javaScript
timerId: setTimeout(() => this.cache.delete(key), duration)

```
setTimeout 的參數是一個回調函數：
- `() => this.cache.delete(key)`：當計時器觸發時，執行刪除操作。
- duration：計時器的延遲時間（以毫秒為單位），指定在多長時間後執行該回調。
- setTimeout 確保鍵在 duration 毫秒後自動過期並刪除。

## 可應用實際案例：
特別適合需要管理短期數據的應用場景，例如緩存用戶會話、臨時配置或頻繁變化的數據

例如，可以使用 TimeLimitedCache 來緩存 API 的回應結果。這樣一來能減少對後端的請求次數。例如，當多個用戶請求相同數據時，可以快速返回緩存結果，而不必每次都發送請求。

```javaScript

import React, { useEffect, useState } from 'react';
// 使用提供的 TimeLimitedCache
import TimeLimitedCache from './timeLimitedCache.js';


// 初始化全局緩存實例
const apiCache = new TimeLimitedCache();

// 將判斷該取得後端 url 的 cache 處理，包為一函式
const fetchWithCache = async (url, ttl = 5000) => {
    const cachedResponse = apiCache.get(url);
    // 檢查指定 URL 是否有未過期的緩存
    // 我們可以使用 TimeLimitedCache 中的 get 方法
    if (cachedResponse !== -1) {
        console.log('Fetching from cache:', url);
        return cachedResponse;
    }

    const response = await fetch(url);
    const data = await response.json();
    // 緩存 API 回應
    apiCache.set(url, data, ttl);
    return data;
};

// React 組件
const App = () => {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        const fetchData = async () => {
            setLoading(true);
            const apiUrl = 'https://jsonplaceholder.typicode.com/posts/1'; // 測試 API
            // 傳入 apiUrl 並設定時間
            const result = await fetchWithCache(apiUrl, 10000); // 10 秒 Time-to-Live
            setData(result);
            setLoading(false);
        };

        fetchData();
    }, []);

    if (loading) return <div>Loading...</div>;

    return (
        <div>
            <h1>Post Details</h1>
            <p><strong>Title:</strong> {data.title}</p>
            <p><strong>Body:</strong> {data.body}</p>
        </div>
    );
};

export default App;

```


## 補充可以使用 class

```javaScript

class TimeLimitedCache {
  constructor() {
    this.cache = new Map();
  }

  set(key, value, duration) {
    const found = this.cache.has(key);

    if (found) {
      clearTimeout(this.cache.get(key).timerId);
    }

    this.cache.set(key, {
      value,
      value,
      timerId: setTimeout(() => {
        this.cache.delete(key);
      }, duration),
    });

    return found;
  }

  get(key) {
    return this.cache.has(key) ? this.cache.get(key).value : -1;
  }

  count() {
    return this.cache.size;
  }
}
```

參考資料
[[Medium] LeetCode JS 30 - 2622. Cache with Time Limit](https://www.explainthis.io/zh-hant/swe/cache-with-time-limit)
[LeetCode JS30-Day17 | 2622. Cache With Time Limit 有時間限制的緩存Cache](https://ithelp.ithome.com.tw/m/articles/10329969)