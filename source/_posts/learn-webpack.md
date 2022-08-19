---
title: 快速認識 webpack
date: 2022-08-19 22:14:18
tags: ["JS","webpack"]
---

經常使用 vue 或 Ｒeact 等前端框架來進行專案開發，但沒有機會好好來認識下他背後的打包工具：webpack
趁著這次六角釋出的基礎 webpack 教學，順道來記錄一下。

## 前端壓縮打包工具
* 從官網的圖，很明顯地就可以看到將左側複雜的檔案進行壓縮，並做項目管理
* 在 Vue , React 的背後都有使用 webpack
![](https://i.imgur.com/EXBNQe0.png)
## 使用 webpack
1. 安裝
 建立 webpack 需要使用到 npm 初始化
 `npm init -y` 
 安裝之後，會產生 `package.jason ` 檔案
2. 安裝 webpack 以及 webpack-cli ，並存在 開發環境
`npm install webpack webpack-cli --save-dev`
 * 會產生 `package-lock.json` => 紀錄版本詳細資訊
 * `node_modules`  資料夾
 
 ![](https://i.imgur.com/qPKyya7.png)

## 進入點(entry)、輸出點(output)
* `/src/index.js`
* `/dist/main.js`
* 指令：`"build": "webpack"` => 設定 webpack 將 src 中的 index.js 檔案輸出，放到 dist 資料夾
	* 使用該指令 `npm run build`
	![](https://i.imgur.com/5biytDZ.png)
  * build 之後產生 dist/main.js

### entry, output 名稱修改
	* 建立 `webpack.config.js` 檔案

```javascript=
const path = require('path');

module.exports = {
  //預設路徑位置
  entry: './src/index.js',
  output: {
    //輸出位置名稱(filename),檔案位置
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
};

//path.resolve(__dirname, 'dist')
// dirname當前資料夾路徑
//path.resolve 將相對路徑改為絕對路徑
```

![](https://i.imgur.com/ORdpSVR.png)

#### 讓每次產生的 bundle 名稱不一樣
![](https://i.imgur.com/5j8tbCO.png)
* 搭配 plugin 來使用 HtmlWebpackPlugin



## NPM script , 自訂指令
* 用 node 執行一段 JS ，位置為同層下的 hello.js
* `npm run hello`
* 網站上線\部署或開發，可能都需要下不同指令來進行管理
![](https://i.imgur.com/4REJGUk.png)

## mode 切換
![](https://i.imgur.com/XlAvPHx.png)

* 不同模式下，會因程式內容而輸出不同內容
* `production` 上線版模式, 輸出的 bundle 檔案會較為精簡
	* 會壓縮優化，將程式碼壓成一行
	* 不容易除錯與理解
![](https://i.imgur.com/KaH0uYg.png)

## CSS loader
* 幫助 webpack 載入 css
* `npm install --save-dev css-loader`
* [CSS loader](https://webpack.js.org/loaders/css-loader/#root)
* `webpack.config.js`
* 別忘了 在載入點加入 ·`import css from './all.css'`

```javascript
const path = require('path');

module.exports = {
  //預設路徑位置
  entry: './src/index.js',
  output: {
    //輸出位置名稱(filename),檔案位置
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js',
  },
  module: {
    rules: [
      {
        test: /\.css$/i, //只要副檔名為 .css 就會使用以下
        use: ["style-loader", "css-loader"],
      },
    ],
  },
};
```

* 裝入 sass-loader
![](https://i.imgur.com/UTFdYXm.png)
[sass-loader](https://github.com/webpack-contrib/sass-loader)

## 載入 webpack 測試伺服器
* 建立即時更新的方式
* [webpack-dev-server](https://github.com/webpack/webpack-dev-server)
* `npm install webpack-dev-server --save-dev`
* 在 `webpack.config.js` 檔案設定
* [dev-server](https://webpack.js.org/configuration/dev-server/#devserver)

```javascript
  devServer: {
    static: {
      directory: path.join(__dirname, 'dist'),
				//contentBase: path.join(__dirname, 'dist'),
    },
    compress: true,
    port: 9000,
		open: true //可以即時更新
  },
```
* 最後，指令調整，如此就可以開始進行編譯
![](https://i.imgur.com/WvIjXoU.png)

* webpack 也可以載入 node_module 下的東西
* 用 dist 下的檔案來部署
## `webpack.config.js` 檔案
* 注意
* mode: 設定development 或是 production
* entry 進入點在哪
* output 輸出去哪
* module -> rule 下 會放 scss-loader , babel-loader 等規則
* plugins: webpack 主要是讀檔案，但透過 plugins ，如建立新的 html檔案, MiniCssExtractPlugin:將 css 從 js 抽離出來
![](https://i.imgur.com/Bn6Jgyp.png)
* devtool: 'source-map' => 協助檔案 debug 



[關於 Webpack，它是什麼？能夠做什麼？為什麼？怎麼做？— freeCodeCamp 的筆記](https://askie.today/what-is-webpack/)
[Webpack 是什麼？模組打包工具的用途及基本 Webpack 教學](https://tw.alphacamp.co/blog/webpack-introduction)
