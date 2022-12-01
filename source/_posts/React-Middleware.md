---
title: Redux Toolkit - RTK Query中間層非同步Fetch API的處理
date: 2022-12-01 12:55:30
tags: ["React"]
categories: React
---


當發起 dispatch 到 UI 更新之間做什麼事情
* 希望 dispath 有能力可以再去做其他事情（所以會在過程中去做一些邏輯）
* 希望過程中可以再做些事情（UI 從 dispatch 到真正渲染之間）

  ![](https://i.imgur.com/FROBvmD.png)
[Middleware](https://redux.js.org/understanding/history-and-design/middleware)

* 實際上是一個 function
* next 是 dispatch 的意思，next 所代表的是傳入 action ，會調用下一個 dispatch 來執行所傳入的這個 action 

以官網範例：

* 他是 3 個函式包在一起

```javaScript
const logger = store => nextDispatch => action => {  
	console.log('dispatching', action)  
	let result = nextDispatch(action)  
	console.log('next state', store.getState())  
	return result  
}
```

在專案建立
位置：src / middleware

### 簡易說明
在 return 最後的 function 之前，第二個 function 就是將原有的 dispatch 丟到最後的 function 中，再 return 出去。
在取的 dispatch 之前，會再拿到 store 也是使用 api 時候會回傳進來的東西。
當 middleware 建立好之後，要到 store 當案進行宣告

### combineReducers
* 建立的 slice夾中可能會有多個 reducer，例如 todoReducer, friendReducer, phoneReduce...


Middleware 與 store 的建立
![](https://i.imgur.com/H7EOAfX.png)

在 middleware 去寫功能的時後，主要分為兩個部分
1. 在 dispach 之前要做什麼事情
2. 在 dispach 之後要做什麼事情

## RTK Query
使用 middileware 去定義我們的 reducers 會分成多個階段
* pedding
* success
* error
針對每一次 API 取資料，加入到  reducers 中，此方式會使得 reducer 變得龐大、複雜。
RTK Query 協助將所有關於 call API 或是 獲取資料過程的功能，將他包裝成一個攻能，並且完全獨立於 reducer 或 redux 的邏輯
* 可以使用 RTK Query 中的 hook 去獲取當前 data , error 狀態等
[官方文件](https://redux-toolkit.js.org/rtk-query/overview)

## 建立 api 

educerPath => 最後會產生 reducer ，所包含內容會有 pedding, success,error 等狀態 
baseQuery => 就是放入 baseUrl
endpoint => 放入 query 資訊
	any : 表示會回傳一個 any 結果
	string : 需要傳入 string 的 input 

json placeholder

![](https://i.imgur.com/40MQaiu.png)


```javascript
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react'

export const todoApiService = createApi({

		reducerPath: 'todoApi',
		
		baseQuery: fetchBaseQuery({ baseUrl: 'https://jsonplaceholder.typicode.com/' }),
		
		endpoints: (builder) => ({
		
		// getPokemonByName: builder.query<Pokemon, string>({
		
		// query: (name) => `pokemon/${name}`,
		
		// }),
		
		getTodoApi: builder.query<any, string>({
		
		query: (id) => `todos/${id}`,

	})

	}),

})
// Export hooks for usage in functional components, which are  
// auto-generated based on the defined endpoints 
// 以下會自動對應產生
export const { useGetTodoApiQuery } = todoApiService
```

## 至  store 新增 todoAPI
![](https://i.imgur.com/ChMTJOz.png)


## 引用 API hook

`import { useGetTodoApiQuery } from './services/todoApi';`

```jsx
import './App.css';
import styled from 'styled-components'
import { useAppSelector, useAppDispatch } from './hooks';
import { addTodo, addTimestamp } from './slice/todo'
import React, { useState } from 'react';
import { useGetTodoApiQuery } from './services/todoApi';

const Wrapper = styled.div`
  padding: 1.5rem;
`

const Title = styled.h2`
  font-weight: 900;
  margin-top: 2rem;
`

const NoteInput = styled.input`
  width: 100%;
  height: 40px;
  border-radius: 10px;
  padding-left: .5rem;
  box-sizing: border-box;
`

const SubmitBtn = styled.button`
  width: 100%;
  box-sizing: border-box;
  height: 40px;
  background: #000;
  display: flex;
  justify-content: center;
  align-items: center;
  color: #fff;
  border-radius: 10px;
  border: 0;
  font-weight: 900;
  margin-top: 1rem;

  :active {
    background: #000000be;
  }
`

const Item = styled.div`
  margin-top: 1rem;

  > b {
    margin-right: .5rem;
  }
`


function App() {
  const todoReducer = useAppSelector(state => state.todoReducer)
  const todoList = todoReducer.todoList
  const dispatch = useAppDispatch()
  const [text, setText] = useState("")
  const { data, error, isLoading } = useGetTodoApiQuery('1')
  //api 獲取資料
  console.log('data:', data)
  console.log('error:', error)
  console.log('isLoading:', isLoading)

  //處裡 data 可能 undefined 的問題
  const { userId = 'N/A', title = 'N/A' } = data || {}

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
      <Title>List2</Title>
      {
        isLoading && <p> 正在載入資料...</p>
      }
      {
        !isLoading &&
        (
          <div>
            {/* <p>User ID:{data?.userId}</p>
            <p>Usr Title:{data?.title}</p> */}
            <p>User ID:{userId}</p>
            <p>Usr Title:{title}</p>

          </div>
        )


      }
    </Wrapper>
  );
}

export default App;

```


若是使用 react thunk 
* 這裡的 middleware 的 reducer 中的 status 要自己去定義
* 優點：可以完全掌控自己 reducer 的內容
* 但相對也會較為繁瑣
