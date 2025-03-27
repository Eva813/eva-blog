---
title: figma mcp 應用
date: 2025-03-27 19:30:14
tags:
---
有新東西就是要來快速嘗試一下～～ 注意 cursor 要是付費版才能使用

至 GitHub clone 專案 [Cursor Talk to Figma MCP](https://github.com/sonnylazuardi/cursor-talk-to-figma-mcp)

然後遵循 README：**Get Started 開始**

它需要安裝 bun 套件管理工具

```jsx
curl -fsSL https://bun.sh/install | bash
```

需要在你的專案根目錄執行:

```jsx
cd /path/to/your/cursor-talk-to-figma-mcp
bun setup
```

```bash
# 啟動 WebSocket server（必須持續執行）
bun socket

# 啟動 MCP server（另開一個終端）
bunx cursor-talk-to-figma-mcp
```

```bash
// Add the server to your Cursor MCP configuration in ~/.cursor/mcp.json:

{
  "mcpServers": {
    "TalkToFigma": {
      "command": "bunx",
      "args": ["cursor-talk-to-figma-mcp"]
    }
  }
}
```

以下為圖示：

![image.png](https://i.imgur.com/6LuXXYv.png)

<!-- more -->

從 Figma 的官網下載 ：https://www.figma.com/downloads/

在第一次設定：選擇左上角的 Figma Icon，點選「Plugins」→「Development」→「Import plugin from manifest」

- 在 Figma 中，點選選單 → **Plugins** → **Development** → Import plugin from manifest
- 指定並選取專案中的 `src/cursor_mcp_plugin/manifest.json` 檔案
- 完成後，該外掛就會出現在你的 Figma Development Plugins 列表中，可以直接使用了。

![image.png](https://i.imgur.com/UZwKie7.png)

此外，要注意 cursor  連線，當你有成功連接 mcp 會有綠點，且呈現 Enable:

![image.png](https://i.imgur.com/ei4uzVB.png)

![image.png](https://i.imgur.com/xysn5c0.png)

確定與 figma 連線成功：

執行：talk fo figma , channel id: {你的 channel id}

![image.png](https://i.imgur.com/YhCxwoq.png)

接著你就可以告訴他你要做得 figma 內容，像是我的指令

```bash
設計一個 管理ai prompt 桌面版，請提出以下方式讓我完成 應用程式 所有需要的設計：

思考使用者需要 管理ai prompt 桌面版 要提供哪些功能:

作為產品經理提出功能規劃

作為設計師提供整體介面設計

可以使用 FrontAwesome 等圖示展示，讓原型圖更接近真實
```

但是他只幫我產出簡單的 layout，要做出一設計稿還是很有大難度

![image.png](https://i.imgur.com/yr4O7rI.png)

參考：
https://muki.tw/figma-mcp-server-cursor/

---

使用 https://github.com/GLips/Figma-Context-MCP 

閱讀 Readme: **Connecting to Cursor**

1.在自己終端機：

```bash
npx figma-developer-mcp --figma-api-key=<your-figma-api-key>
```

- -figma-api-key 要到 figma 登入後，點擊頭像 settings →  security → create new token

2.到 **Cursor → Cursor settings → mcp**

```bash
{
  "mcpServers": {
    "Framelink Figma MCP": {
      "url": "https://localhost:3000/sse"
    }
  }
}
```

3.可以回到 figma ，要選擇你要的畫面

![image.png](https://i.imgur.com/Lbv0HvC.png)

注意你在本地，要先建立好一 react 專案

```bash
npm create vite@latest my-react-app -- --template react
```

然後到 cursor → cd my-react-app  →  chat 貼上：

```bash
@https://www.figma.com/design/lImjbdZmdV25TQ23glGfvV/Locofy-Sample-Project---FickleFlight-(Community)?node-id=1003-3841&t=CNAo85NCrYNAJpJ6-4 
請幫我用 tailwindcss做此畫面， tailwind css 設定我已經處理好，請專注於做畫面，不要用其他 css框架，就全部使用tailwind，記得圖片也要抓下來
```

在該 cursor 專案：

![image.png](https://i.imgur.com/ADYZghc.png)

以下，可以看到他做的切版：

我參照的 figma template : Locofy Sample Project - FickleFlight (Community)

![image.png](https://i.imgur.com/L1PUGuw.png)

![image.png](https://i.imgur.com/yMcOvmL.png)

