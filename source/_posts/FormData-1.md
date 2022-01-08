---
title: FormData 表單的運用
date: 2022-01-6 10:44:33
tags: ["JavaScript","TypeScript"]
---

![](https://i.imgur.com/xf0DAqA.png)

最近遇到將表單資料存到後端的方式，過去一直以為是將資料整理為物件方式存取即可，後來發現使用FormData來建立物件傳送檔案即可!

* FormData 是一個表單格式，利用它將資料轉成表單的格式，並且以表單的形式回傳給後端

## 建立 FormData 物件
* 最直接方式，先取得form的資料

```javascript
// 根據表單元素取得表單資料
var form = document.querySelector('form');
var formData = new FormData(form);

// 可以用 append 把欄位灌進去表單中
var formData = new FormData(); // 一開始表單的資料是空的
formData.append('username', Chris); // 增加欄位
formData.append('image', file); // 檔案也可以透過 append 放進來
```
## 以下介紹幾個基礎的使用方式：

* `formData.append(name, value)` : 加入有 name 和 value 的表單資料(name:表單標籤的名稱,value: input 輸入的值)
* `formData.append(name, blob, fileName)` : 加入字段，就像它是 ``<input type="file">``，第三個參數 fileName 設置檔案名
* `formData.delete(name)` : 移除帶有 name 的字段，
* `formData.get(name)` : 取得帶有 name 的值，
* `formData.has(name)` : 如果帶有給定 name 的字段，返回 true，否則返回 false。

## 使用axios 傳遞資料
```javascript
var formData = new FormData();
//宣告要取得的值
var title = form.name
formData.append("formTitle", title);
axios.post('upload_file', formData, {
    headers: {
      'Content-Type': 'multipart/form-data'
    }
})
```

## 補充 vue+Typescript 宣告型別
* 以 stack overflow 內的資料為例：
	* 可以使用 ref來取得DOM元素，並宣告該變數為HTMLFormElement
```javascript
 <el-form
          class="m-auto"
          ref="formRef"
          :model="form"
          :rules="rules"
        >
```
```javascript
const formRef = ref<HTMLFormElement>();
```
* 另是使用 element-UI 的表單，也可以使用
`import {  ElForm } from "element-ui";`

[如何使用jQuery AJAX submit 傳送form表單方法](https://www.ucamc.com/articles/332-%E5%A6%82%E4%BD%95%E4%BD%BF%E7%94%A8jquery-ajax-submit-%E5%82%B3%E9%80%81form%E8%A1%A8%E5%96%AEserialize-%E6%96%B9%E6%B3%95)
[JavaScript / jQuery 前端開發入門實戰
](https://5xruby.tw/courses/javascript)
[JavaScript Info-formData](https://zh.javascript.info/formdata)
[stackoverflow](https://stackoverflow.com/questions/67074871/vue-3-typescript-unable-to-access-method-from-ref)
