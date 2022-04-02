---
title: websocket 簡介與使用
date: 2022-04-02 16:49:04
tags: ["websocket"] 
---

![](https://i.imgur.com/piRytMY.png)

近期購買了布魯斯的 TypeScript 的課程，除了學到型別之外，還接觸到 ＷebSocket 的使用，所以就來簡單紀錄下 ＷebSocket 是什麼，在專案內可以實現什麼功能。

WebSocket是一種網路傳輸協定，讓前端與後端溝通除非有一方斷開連接，否則會一直保持聯繫

## 情境說明
1. 使用者上下線狀態：可以延遲幾秒鐘，可以使用 setInterval 做出輪循
* 設定每幾秒發出請求，更新狀態
* 使用 HTTP 協議的請求：GET\POST 等方式，做出Request\Response
2. 聊天室：會希望聊天訊息是即時更新，若採用上面的方式，為了使訊息更新及時會從每幾秒改為每幾毫秒，這樣的方式會造成短時間內不斷發出請求，造成消耗後端資源
* 指建立一次連結，可以與後端持續保持聯係

## 聊天室的實際運用
* 將在 node.js 建立 websocket 的服務，讓前端連線到node.js的服務
* socket.io => 包裝 websocket 協議的 node.js 的工具包
	*  當使用 node.js 做開發，就可以透過 socket.io 提供的功能實現

## 安裝 socket.io
* socket.io-clinet : 在前端的部分要使用 clinet 安裝包
`npm i -D socket.io socket.io-clinet`

### 後端
* 將 socket.io 引入 node.js 的 server 端

```javascript
import devServer from "@/server/dev";
import prodServer from "@/server/prod";
import express from "express";
import { Server } from "socket.io"
import http from "http"  //也可以引入http模組

const port = 3000;
const app = express();
//透過 http 將 server 建立起來
const server = http.createServer(app)
const io = new Server(server)
const userService = new UserService

//監聽
server.listen(port, () => {
  console.log(`The application is running on port ${port}.`);
});

```

```typescript
//建立起連接時，發出訊息
//當有用戶連接到connection 就會有回呼函式
io.on('connection',(socket)=>{
	//發出訊息
	socket.emit('join', 'Welcom')
})
```


## 如何在前端使用，與後端建立連線？
* 到前端的入口檔案

```typescript
//將 socket.io 的包引入前端程式這邊
import { io } from "socket.io-client";

//1. 建立連接到 node server
const clientIo = io();


clientIo.on('join',(msg)=>{
	console.log('msg',msg)
})
```
