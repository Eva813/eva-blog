---
title: React 測試 - 以登入元件練習
date: 2022-11-02 20:49:15
tags: ["React"]
categories: React 測試
---

想找專案練習來更認識測試的運用，至 youtube 找尋測試相關的教學。

### 建立 login 檔案
* login.js : 製作登入元件
* login.test.js : 撰寫測試

 ![](https://i.imgur.com/4Pw5JTD.png)

### 確認 輸入框 有渲染至文件
* 使用 `getByPlaceholderText` 取得輸入框元素

```html
 <form>
        <input
          type="text"
          placeholder="username"
          value=''
        />
        <input
          type="password"
          placeholder="password"
          value=''
        />
  </form>
```

```javascript
  test("username input should be rendered", () => {
    render(<Login />);
    const usernameInputEl = screen.getByPlaceholderText(/username/i);
    expect(usernameInputEl).toBeInTheDocument();
  });

  test("password input should be rendered", () => {
    render(<Login />);
    const passwordInputEl = screen.getByPlaceholderText(/password/i);
    expect(passwordInputEl).toBeInTheDocument();
  });
```

### 確認畫面上有按鈕

* getByRole 取得按鈕

```javascript
  test("button should be rendered", () => {
    render(<Login />);
    const buttonEl = screen.getByRole("button");
    expect(buttonEl).toBeInTheDocument();
  });
```
### 輸入框一開始為空

```javascript
test("username input should be empty", () => {
  render(<Login />);
  const usernameInputEl = screen.getByPlaceholderText(/username/i);
  expect(usernameInputEl.value).toBe("");
});

test("password input should be empty", () => {
  render(<Login />);
  const passwordInputEl = screen.getByPlaceholderText(/password/i);
  expect(passwordInputEl.value).toBe("");
});
```
### 輸入框為空時，按鈕應為 disabled

* 只要取得按鈕元素並使用 `toBeDisabled()`

```javascript
test("button should be disabled", () => {
  render(<Login />);
  const buttonEl = screen.getByRole("button");
  expect(buttonEl).toBeDisabled();
});
```
### 錯誤訊息的提示

![](https://i.imgur.com/hQ33Kxa.png)

* 確保一開始進入畫面，該訊息不應該出現
* 可以看見 `toBeVisible()` ，反之期望無法看見，就需加入not

```javascript
test("error message should not be visible", () => {
  render(<Login />);
  const errorEl = screen.getByTestId("error");
  expect(errorEl).not.toBeVisible();
});
```

### 輸入框的輸入事件

* [fireEvent](https://testing-library.com/docs/dom-testing-library/api-events/) 用來針對事件進行測試

![](https://i.imgur.com/E9wT8gO.png)


```javascript
test("username input should change", () => {
  render(<Login />);
  const usernameInputEl = screen.getByPlaceholderText(/username/i);
  //模擬傳入的值
  const testValue = "test";

  fireEvent.change(usernameInputEl, { target: { value: testValue } });
  expect(usernameInputEl.value).toBe(testValue);
});

test("password input should change", () => {
  render(<Login />);
  const passwordInputEl = screen.getByPlaceholderText(/password/i);
  const testValue = "test";

  fireEvent.change(passwordInputEl, { target: { value: testValue } });
  expect(passwordInputEl.value).toBe(testValue);
});
```

### 當輸入框有值的時候，按鈕不應該 `disabled`

* 期望按鈕可以點擊
* 在 jsx 檔案內要設置在輸入框沒有值的時候才是 disabled

![](https://i.imgur.com/MdNkcGo.png)


```javascript

test("button should not be disabled when inputs exist", () => {
  render(<Login />);
  const buttonEl = screen.getByRole("button");
  const usernameInputEl = screen.getByPlaceholderText(/username/i);
  const passwordInputEl = screen.getByPlaceholderText(/password/i);

  const testValue = "test";

  fireEvent.change(usernameInputEl, { target: { value: testValue } });
  fireEvent.change(passwordInputEl, { target: { value: testValue } });

  expect(buttonEl).not.toBeDisabled();
});
```


#### css 補充

```css
  style={{ visibility: error ? "visible" : "hidden" }}
```

類似： display:none和visibility:hidden

而兩者差異：
visibility：的隱藏，是物件的位置仍舊保持著不會消失,
display: none, 會將 html 該物件拔除

## 小結

此練習範例，很請楚地知道在建立一個功能元件之後，如何再加入每一個區塊的測試，如標題文字、輸入框、按鈕的測試等等，也可以觀察到測試根據使用情境一步步的拆解並加入測試
下一篇會有串接資料搭配測試，敬請期待


參考資料
[github-login](https://github.com/safak/youtube2022/blob/react-testing/src/components/login/Login.jsx)
[React Testing Tutorial with React Testing Library and Jest](https://www.youtube.com/watch?v=Flo268xRpV0)
