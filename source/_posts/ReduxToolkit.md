---
title: Redux Toolkit
date: 2022-12-01 12:03:04
tags: ["React"]
categories: React
---

安裝：
[官網安裝](https://redux.js.org/introduction/installation)

此範例來自 Bruce 前端課程，使用 todoList 來練習
* 從官網學習基礎設定
1. 建立 store 同時，撰寫 reducer(會自動產生出對應 reducer 的 action)
	1. action 如同 dispatch 所送出的包裹
	2. reducer 會去拆解包裹，以了解用戶想做的事
應用於 todo-list
1. store 中會存有幾條備忘錄，幾條 todo視像要做
2. 建立資料夾 src / slice ，此 slice 中包含 reducer, action 
[TS-設立tool-kit](https://redux.js.org/tutorials/typescript-quick-start)
建立 store 的方式，要先建立 reducer

### src / slice / todo.ts
* 建立 slice，並注意要傳入三個參數
	* name : slice 的名字
	* initialState 初始化的 state，建立初始狀態得值以及型別

```javaScript 
import { createSlice } from '@reduxjs/toolkit'
// Define a type for the slice state  
interface todoState {  
  todoList: Array<string>
}
const initialState: todoState = {  
  todoList: []  
}
export const todoSlice = createSlice({  
	name:'todo',
	initialState,
	reducers:{
		//state:當下狀態
		//action:對應的動作
		addTodo: (state,action)=>{
			state.todoList.push(action.payload)
		},
		addTimestamp:(state)=>{
			state.todoList.push(Date.now().toString())
		}
		
	}
}  
})
```


在此範例中會有兩個 dispatch
1. submit
2. record timestamp
* dispatch 會發出上面兩個 action 
* reducer 要兩個事件接收
最後的 export todoSlice
* todoSlice 中會包含 actions , reducer , 以及其他 API

## 建立 store
1. 位置 src / store.ts
```javascript
import { configureStore } from '@reduxjs/toolkit'
```

![](https://i.imgur.com/41RnyWT.png)

從上面過程，可以看到我們 store, action , reducer 都撰寫完成

## 使用
* 回到 UI 觸發狀態改變的地方 
* 1. index.tsx 裝入 store
	* 像是 context 的概念作為 context provider 的內容
  
```javaScript
import { Provider } from "react-redux";
import store from './store'
ReactDOM.render(

	<React.StrictMode>
		
		<Provider store={store}>
		
			<App />
		
		</Provider>
	
	</React.StrictMode>,
	
	document.getElementById('root')

);
```

拿到 store 中的 todoList 來使用渲染於畫面

* 要如何拿到 store ? 使用 useSelector
* 在這裡要注意因為使用 TS 所以會有類型定義問題！
	*  src / hooks.tsx
1.  型別首先要來自於 store 檔案
* ReturnType: 幫我們將 store 中的內容，直接導出對應的 type

`export type RootState = ReturnType<typeof store.getState>`

2.  回到 hooks 建立定義好型別的 API

``` javaScript
export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector
```
3. 可以 到 App 檔案或相關的子組件都可以調用 store 的內容
	* 可以拿到預設的 state 

```javascript
import { useAppSelector } from './hooks';
function App() {

const todoReducer = useAppSelector(state => state.todoReducer)

const todoList = todoReducer.todoList


return (

<Wrapper>

	<Title>TODO LIST</Title>
	
	<NoteInput type="text" />
	
	<SubmitBtn>
	
		Submit
	
	</SubmitBtn>
	
	<SubmitBtn>
	
		Record Timestamp
	
	</SubmitBtn>
	
	<Title>List</Title>
	
	{
	
	todoList.map((todo, index) => {
		
		return <Item key={todo}>
		
			<b>{index + 1}</b>
		
			{todo}
		
		</Item>
		
		})
	
	}

</Wrapper>

);

}

  

export default App;
```

### 狀態更新
* 使用 dispatch 一些 action
* 定義 useDispatch 
1. hooks
```javascript
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux'

import type { RootState, AppDispatch } from './store'


//自定義 hook

// Use throughout your app instead of plain `useDispatch` and `useSelector`

export const useAppDispatch: () => AppDispatch = useDispatch

export const useAppSelector: TypedUseSelectorHook<RootState> = useSelector
```
2. store.ts

```javascript
import { configureStore } from '@reduxjs/toolkit'

import todoReducer from './slice/todo'

const store = configureStore({

	reducer: {
	
		todoReducer
		
		}

})


// Infer the `RootState` and `AppDispatch` types from the store itself

//匯出對應的類型
export type RootState = ReturnType<typeof store.getState>

export type AppDispatch = typeof store.dispatch

export default store
```

到 App.tsx 使用 dispatch
* 宣告 dispatch
* 設置 setState 的值
* 綁定於 input 並讓 input onchange 時去觸發值得改變

```javascript
import './App.css';

import styled from 'styled-components'

import { useAppSelector, useAppDispatch } from './hooks';

import { addTodo, addTimestamp } from './slice/todo'

import React, { useState } from 'react';

  
function App() {

const todoReducer = useAppSelector(state => state.todoReducer)

const todoList = todoReducer.todoList

const dispatch = useAppDispatch()

const [text, setText] = useState("")

return (

<Wrapper>

	<Title>TODO LIST</Title>
	
	<NoteInput type="text" value={text} onChange={(e: React.ChangeEvent<HTMLInputElement>) => {
	
	setText(e.target.value)
	
	}} />
	
	<SubmitBtn onClick={() => {
	
		if (text === '') {
		
		alert('請輸入TODO內容')
		
		return
		
		}
	
		dispatch(addTodo(text))
	
		setText('')
	
	}}>
	
		Submit
	
	</SubmitBtn>
	
	<SubmitBtn onClick={() => {
	
		dispatch(addTimestamp())
	
	}}>
	
		Record Timestamp
	
	</SubmitBtn>
	
	<Title>List</Title>
	
	{
	
		todoList.map((todo, index) => {
		
		return <Item key={todo}>
		
			<b>{index + 1}</b>
			
			{todo}
		
		</Item>
	
	})
	
	}

</Wrapper>

);

}

  

export default App;
```

## 總結步驟
1. 定義 slice 中的內容，其中包含 初始的資料定義、 reducers
	1.  reducers 會透過 toolkit 這工具，將對應得 action 產生出來
	2. action 可以在 dispatch 做發送時帶出去
2. 定義 provider 作為將 store 資料傳入的橋樑
3. store 的產生：來自於將 slice 中的 reducers 建立好之後可以隨之建立的內容
4. 建立 hooks ：撰寫自定義的 hook ，將原本內建的 hook 進行重新包裝
