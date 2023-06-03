---
title: cypress 學習筆記 - css Locator
date: 2023-06-03 09:48:47
tags: ["test" , "cypress"]
categories: E2E
---

有空就找找實作影片來練習一下～

而今天的實作練習，訪問 momoshop 取得搜尋框元素 -> 輸入搜尋的關鍵字("雨傘") -> 點擊搜尋 -> 
查看搜尋後的項目是否包含關鍵字。



```JS
describe('CSSLocators',() => {
  it("csslocators", ()=>{
    cy.visit("https://www.momoshop.com.tw/main/Main.jsp?momo=1&gclid=CjwKCAjwpuajBhBpEiwA_ZtfhU9MqvxnVs7Kh8LTNDbmKzFsmDxY16QIaP7lci98eVgfRoNz8ychuRoCao0QAvD_BwE")
    
    //尋找某些關鍵字
    cy.get('[name="keyword"]').type("雨傘")
    //點擊搜尋按鈕
    cy.get("[title = '搜尋']").click()  //attribute
    cy.get('.prdName').contains('傘')
    cy.get('.prdName').contains('羽絨') //Assertion
  })
})
```

![](https://hackmd.io/_uploads/BytHdzdU2.png)

在此文章主要針對取得 `css Locator` 進行系列操作。
可以使用 `#idName`, `.cssName`, `[title = '搜尋']`, `[name = 'search']` 等方式
其實 cypress 官方網站也滿容易讀，可以根據我們想要在網頁上進行何種操作，搜尋相應的功能。
例如: Assertion [cypress-assertions](https://docs.cypress.io/guides/references/assertions)

小補充另一個取得定位的方式：
XPath：
  XPath是一種用於導航XML文件的語言，同樣也適用於HTML文件。它提供了一種根據元素在文檔層次結構中的路徑選擇元素的方法。XPath表達式非常強大且靈活，可以根據元素的屬性、文本內容等條件進行選擇。

在Cypress中，您可以使用XPath選擇器，只需將選擇器字符串以 xpath 為前綴添加到命令中。例如：

```JS
  cy.xpath('//button[@id="submit-btn"]').click();
```
