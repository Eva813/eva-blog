---
title: React 測試
date: 2022-11-02 15:43:21
tags: ["React"]
---

使用 JavaSript 要進行測試最常使用的就是使用 Jest ，另外依據使用的前端框架來搭配使用進行測試。
以下內文就先以一些範例來直接學習

## testing-library
* 當使創建一個 react 檔案，會預設加入的測試範例檔
```javascript
 test('renders learn react link', () => {
  const linkElement = screen.getByText(/learn react/i);
  expect(linkElement).toBeInTheDocument();
});
```

* screen  : it represent the whole document which we have rendered
* getByText: 取得 text
	* `getByText(/learn react/i)`
	* `expect(linkElement).toBeInTheDocument();`
	* 期待變數得值，`toBeInTheDocument()`
	* 檢測此項目是否存在於文件之中
	* -   [toBeInTheDocument](https://github.com/testing-library/jest-dom#tobeinthedocument)：判斷一個 Element 是否存在於 DOM 中

### 撰寫測試的參考文件

[ByRole](https://testing-library.com/docs/queries/byrole)
[ role attribute](https://www.w3.org/TR/html-aria/#docconformance)

### 加入列表（list）測試

```jsx
import logo from './logo.svg';

import './App.css';

import Login from './components/Login/Login';

  

function App() {


	
	return (
	
	<div className="App">
	
		<header className="App-header">
			
			<img src={logo} className="App-logo" alt="logo" />
			
			<p>
			
			Edit <code>src/App.js</code> and save to reload.
			
			</p>
		
			<a
			
			className="App-link"
			
			href="https://reactjs.org"
			
			target="_blank"
			
			rel="noopener noreferrer"
	 .     >					
			
				Learn React
			
			</a>
	
			<ul>
				
				<li>Apple</li>
				
				<li>Banana</li>
				
				<li>Kiwi</li>
			
			</ul>
		
	
		</header>

	</div>

);

}
  
export default App;
```

```javascript
test('renders 3 list items', () => {
  render(<App />);
  const listItems = screen.getAllByRole("listitem");
  expect(listItems.length).toEqual(3);
});
```

* jest [expect](https://jestjs.io/docs/expect)
* 需要時到官網看相關使用的API
`expect(listItems.length).toBe(3);`
`expect(listItems.length).toEqual(3);`

### 測試 title
* getByTestId：運用此方法可以直接取得在元素上設置的 data-testid
* getByTitle : 取得在標籤設置 title 的元素

```html
  <!-- const a = 2
  const b = 1 -->

     <h1 data-testid="mytestid">Hello</h1>
      <span title="sum">{a + b}</span>
```

```javascript
test('renders title', () => {

	render(<App />);
	const title = screen.getByTestId("mytestid");
	expect(title).toBeInTheDocument();

});

test('sum should be 3', () => {
  render(<App />);
  const titleSum = screen.getByTitle("sum");
  expect(titleSum.textContent).toBe("3");
});
```


### 運行測試

`npm run test`
* 透過測試所提供的錯誤提示，可以得知預期的結果以及實際運行後得到的值之間的差異

 ![](https://i.imgur.com/IT8leGK.png)


參考資料：
[[Day29] React Testing Library 的一些實用的小技巧](https://ithelp.ithome.com.tw/articles/10281691)
[Next.js | 初探單元測試，使用 Jest + React Testing Library](https://medium.com/%E6%89%8B%E5%AF%AB%E7%AD%86%E8%A8%98/nextjs-testing-tutorial-1-ed4b27563761)
