---
title: cypress 學習筆記 - Assertion
date: 2023-06-03 19:12:32
tags: ["test" , "cypress"]
categories: E2E
---

## implicit assertion（隱含斷言）

常用的 `should` 關鍵字：

1.  `be.visible`：確保元素在畫面上可見。
2.  `be.hidden`：確保元素在畫面上隱藏。
3.  `be.checked`：確保複選框或單選框元素被選中。
4.  `be.disabled`：確保元素被禁用。
5.  `have.text`：檢查元素的文字內容是否符合預期。
6.  `have.value`：檢查輸入元素的值是否符合預期。
7.  `have.attr`：檢查元素的特定屬性值是否符合預期。
8.  `have.class`：檢查元素是否具有特定的類名。
9.  `contain`：檢查元素是否包含指定的文字內容。

可根據具體需求進行選擇和使用
[keywords](https://docs.cypress.io/guides/references/assertions)

### 這裡的練習範例，驗證當前網址（URL）的斷言方法。

include：確保當前網址包含特定的子字串。

```javascript
cy.url().should('include', '/login');

```

eq：確保當前網址與預期值完全相等。

```javascript
cy.url().should('eq', 'https://example.com/dashboard');

```

contain：檢查當前網址是否包含特定的字串。

```javascript
cy.url().should('contain', 'example.com');
```

這些斷言方法可用於驗證當前網址是否符合預期，從而確保導航或操作正確導致了預期的網址變化。

```JS
describe("Assertion practice",()=>{

	it("explicit", ()=>{
	
		cy.visit('https://opensource-demo.orangehrmlive.com/web/index.php/auth/login')
		
		//should + keyword end
		
		cy.url().should('include', 'orangehrmlive.com')
		
		cy.url().should('eq', 'https://opensource-demo.orangehrmlive.com/web/index.php/auth/login')
		
		cy.url().should('contain', 'orangehrmlive')
		
	
	})

})

```

* 可以看到上述，有連續使用到 `url()`

```JS
cy.url().should('include', 'orangehrmlive.com')

.should('eq', 'https://opensource-demo.orangehrmlive.com/web/index.php/auth/login')

.should('contain', 'orangehrmlive')
```

```JS
	cy.url().should('include', 'orangehrmlive.com')
	
	.and('eq', 'https://opensource-demo.orangehrmlive.com/web/index.php/auth/login')
	
	.and('contain', 'orangehrmlive')
```

另外可以檢查進到頁面 logo 圖示的顯示

```JS

describe("Assertion practice",()=>{
  it("explicit", ()=>{
    cy.visit('https://opensource-demo.orangehrmlive.com/web/index.php/auth/login')
      //check logo 呈現與否
    cy.get('.orangehrm-login-branding > img').should('be.visible')
    .and('exist')
  })
})

```

* 使用者輸入框

```JS

describe("Assertion practice",()=>{
  it("explicit", ()=>{
    cy.visit('https://opensource-demo.orangehrmlive.com/web/index.php/auth/login')
   
    //檢查 username 得輸入
    //取得 username 的輸入框,並且確保輸入的值
    cy.get("input[placeholder='Username']").type("Admin")
    cy.get("input[placeholder='Username']").should("have.value", "Admin")

    
  })
})

```


## explicit assertion
（顯式斷言）」是指明確使用斷言方法來檢查特定的條件或預期結果。
與隱含斷言不同，顯式斷言需要您明確指定斷言方法來進行驗證。
