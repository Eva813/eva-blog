---
title: 自動化測試 E2E - cypress
date: 2023-05-07 12:48:47
tags: ["test" , "cypress"]
categories: E2E
---

![](https://i.imgur.com/AE7iJQ1.png)


每次在交付完成的任務給 PM 之前，總是會先自行反覆點擊測驗過才提能安心提供。但除了自己點擊之外，是否有其他更為簡易的方式可以進行測試呢？Cypress 是一個用於編寫端對端測試的 JavaScript 測試框架。他可以幫助我們做到基礎的自動化測試，今天就先來認識看看吧～

## 建立專案
在本地建立一個 cypress-project 資料夾，進入該檔案夾
`npm init -y`
`npm install cypress`

* 安裝完後
```javaScript
{
  "name": "cypress-project-1",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "cypress": "^12.11.0"
  }
}
```


## 實作測試功能
* 訪問 google 頁面
* 進行關鍵字搜尋

![](https://i.imgur.com/2FOKkBZ.png)

### 測試語法

```javaScript
  it('google search', () => {
    //visit 進入不同網站
    cy.visit('https://www.google.com.tw/?hl=zh_TW')

    cy.get('#APjFqb').type('what is cypress.io{Enter}')
    //除了點擊搜尋按鈕之外，一般也會按下 enter鍵觸發
    // cy.contains('Google 搜尋').click()
  })
```

### 測試操作

`npx cypress open ` 進入測試介面，選擇 E2E

![](https://i.imgur.com/rmkiayJ.png)

* 選擇要測試的檔案：

![](https://i.imgur.com/W6yP5nu.png)

* 透過選取器可以找到相對應的元素，並針對該元素下指令
  * 這邊我們就是拿到 搜尋框，並進行關鍵字的輸入
![](https://i.imgur.com/RZ5EjKZ.png)


### 測試結果呈現
* 會進到 google 頁面
* 在輸入框輸入 what is cypress.io ,點擊 Enter 搜尋

![](https://i.imgur.com/a37BYX1.png)


![](https://i.imgur.com/8QbuLdQ.jpg)


小結：

cy.visit(url)：訪問指定網址。
cy.get()：使用 cy.get() 方法選擇元素。我們可以使用不同的選擇器（如：CSS 選擇器）來指向特定元素。
type()： 使用 type() 方法在輸入框中輸入內容。
click()：使用 cy.get() 定位到這些元素，然後使用 click() 方法模擬點擊操作。

