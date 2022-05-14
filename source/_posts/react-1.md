---
title: React- props / state 練習
date: 2022-05-08 15:42:36
tags: ["React"]
---



## props, state 的應用
* props 傳入參數的部分
* state 讓組建本身有狀態

### 製作 計數器
* props
* 製作，讓計數器的數字變化，可以同步變化到 button 上的數字
	* 在父層 App.vue 做一個內容的傳送
	* 在 子元件，要自訂一個變數，作為接收父層從來的資料

```jsx
import "./styles.css";

type BtnProps = {
  btnNum: number;
};

const Btn: React.FC<BtnProps> = ({ btnNum }) => {
  return (
    <button>
      add 1<span>目前總數：{btnNum}</span>
    </button>
  );
};

export default function App() {
  const num = 0;
  return (
    <div className="App">
      <h1>Counter: {num}</h1>

      <Btn btnNum={num} />
    </div>
  );
}
```


![](https://i.imgur.com/OnOdTAs.png)
![](https://i.imgur.com/8VkhCGp.png)

### 進階，希望透過點擊 button 同步更改數值
* 讓 render (react 渲染機制)，可以在變數被變動的時候順便觸發渲染機制
	* 改變變數 -> 觸發渲染
### 使用 state 達成上述目的

 **對組建本身引入 state 機制**
 
* 用 state 來做變數宣告，只要 state 變數有做變動都會觸發渲染機制
	* 當變數有涉及於頁面上的更新，就會需要使用 state
* Hook 本身多是 use 開頭
* ``useState()`` 是一個 function

**用陣列解構來取的 return 的值**

* 1. 當前的值
* 2. 再回傳一個 function : 用來設定/變動 第一個數值 
``const [num2, setNum2] = useState(999)``

[example-1](https://codesandbox.io/s/react-props-state-yuc6qv?file=/src/App.tsx)

## 實現 父層數值變化，子組件隨之變化
* 應該將主要的更新動作都放置父組件
* 將放在父組件的事件，透過 props 方式傳給子組件，並將該變數放到按鈕的點擊事件上


[example-2 ](https://codesandbox.io/s/react-props-state-2-ylm345?file=/src/App.tsx)
