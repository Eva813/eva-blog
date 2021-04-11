---
title: git介紹-1
date: 2020-11-21 16:34:36
tags: git 
---
git是用來，讓大家對於自己的程式碼做版本控制「Version Control System」
>>也如同：save load 大法
 S是Save 存檔，L是Load 讀取;如：每天的工作可能都是每天新增、編輯、修改許多檔案，而git會紀錄每次檔案的修改資料

另外也是分散式控制系統「Distributed Version Control Systems」
>>可以和不同群組的人使用不同的方式，在同一個專案內協同合作。可讓專案主導者更精準的控制大專案內的小專案。

## 安裝Git圖形介面工具
透過圖形介面工具（GUI, Graphic User Interface）可以幫助使用者熟悉Git的操作指令。常見的有SourceTree 以及 GitHub Desktop 這兩款。
這裏以SourceTree作為範例：
[下載點](https://www.sourcetreeapp.com/)
[Sourcetree的下載與操作](https://ithelp.ithome.com.tw/articles/10206852)
  ***載完後的介面***
![](https://i.imgur.com/04TzJ0s.png)

## 創建第一個 Git Repository
 ***先呈現終端機的指令***

* 先確認狀態 `git status`(表示還沒有git)
* 創建資料夾 `mkdir hello_git`
* 進入資料夾(hello_git)，並初始化 `git init`
* 初始完成囉，出現 master標籤
* 再次確認狀態

![](https://i.imgur.com/kneVHZx.png)
![](https://i.imgur.com/URJ3bOu.png)


### ***可以詳細看一下資料夾內部***
![](https://i.imgur.com/hT0CUMo.png)
* 透過`ls -a`可以看到git的隱藏檔

### 創建完成的同時，SourceTree也會有圖形介面可以看喔
![](https://i.imgur.com/59As6HT.png)

以上就是創建Git Repository 的最初步驟

--- 

## 熟悉 Git 基本指令 
#### 創建hello_git資料夾後，在裡面建立hello.txt
![](https://i.imgur.com/SD39uTZ.png)

#### 進入vs code，輸入hello world 
  ***注意檔案變綠色***
![](https://i.imgur.com/bdKBKX3.png)
 ***查看git status,會是紅色***
 ![](https://i.imgur.com/vjDWNJh.png)

### 接下來 `git add`
 ![](https://i.imgur.com/PSUh7Zc.png)
* 使用git add ＋檔案，並看狀態後會發現變成綠色
* 或git add. 加入資料夾內所有的檔案
![](https://i.imgur.com/dtVWtYp.png)


### 接下來 `git commit`
git commit -m "add new file hello.txt"
* 要注意 -m，以免進入vim
* " "引號內輸入這次要儲存的動作說明，如新增檔案、修改資料等說明
***有一個檔案改變,一行的資料修正***
![](https://i.imgur.com/8O82KJE.png)

查看狀態
 nothing to commit, working tree clean，表示已把檔案加入完成
![](https://i.imgur.com/V8fUljG.png)

### Sourcetree
 ***可以看到歷史紀錄***
![](https://i.imgur.com/OUFvghP.png)

---

## 再來練習一次
#### 在vs code裡，輸入hello,linda
![](https://i.imgur.com/IA3JOLy.png)
![](https://i.imgur.com/Emcv1oF.png)
![](https://i.imgur.com/gH1cFEV.png)

***同樣sourcetree 也會有紀錄***
![](https://i.imgur.com/57n8uaP.png)
