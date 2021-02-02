---
layout: 
title: 新手入門必學Command Line
date: 2021-02-02 22:10:38
tags: Command Line
---

學習Command Line可以幫助我們呼叫檔案、刪除檔案更加快速，此外也可以很快的查看資料夾內的所有檔案，不用透過電腦圖示一個個點選。透過指令的使用，可以更有效率，操作也可以更加廣泛
當然，除了上述的便利性之外，它還能夠有許多操作，下面會介紹一些基本指令。

## Command Line 常用指令介紹

tip: 搜尋：linux cheat sheet，就可以查詢到指令的使用方法。(如：cd:change director (移動到...資料夾))



1. `pwd` 現在位置
    pwd --help 會提供詳細使用資訊
    ![](https://i.imgur.com/VQgzyhw.png)

3. `ls` 列出資料夾內的東西
    `ls -l` 列出詳細資料
    `ls -s` 
    `ll` 列出檔案權限的使用者
    ![](https://i.imgur.com/39J68tH.png)


4. `touch` 創建檔案
    ![](https://i.imgur.com/1WbeaVG.png)
5. `cd` 移動到..地方
6. `cd..` 回到上個資料夾
7. `q` 離開
8. `ctrl +c` 跳出進行的程序
9. `mkdir`  hello (make dir) 建立目錄(資料夾) 名稱為hello
    ***創建好的資料夾會放在..***
    ![](https://i.imgur.com/ZUFmHQs.png)
    
   ![](https://i.imgur.com/Jqscc2A.png)
### 刪除
1. `rm` index.html (指將整個檔案砍掉 不指是到垃圾桶)
2. `rmdir` 刪除目錄
3. `rm -rf`  強制刪除
    ![](https://i.imgur.com/HJeovVl.png)

### 複製、命名與移動
1.  `cp` hello.txt(複製檔案)  
2.   `mv` hello.txt I_am_dir/  （將hello.txt 移到I_am_dir）
    也可以用來改名 mv hello_word.txt hello_luka.txt (將hello_word.txt改名為hello_luka.txt)
    ![](https://i.imgur.com/Wx6oGkI.png)

### 系統管理相關指令
1. sudo:使用最高權限（superuser）執行指令
2. kill：根據 Process ID 指定要終止程式
3. `open .` 可開啟檔案總管
4. code.  開啟 vs code
### 其他補充
1.  `cat` 列出檔案內的內容
2.   `who` 顯示電腦user的名稱
3.   `clear` 清除終端機畫面的指令

## TLDR
   
 安裝 `npm install -g tldr`
 安裝之後，輸入 `tldr  ls ` 就會說明指令的使用
 
 
 
 
 
 
 參考資料：
 https://blog.techbridge.cc/2017/12/23/linux-commnd-line-tutorial/
 https://medium.com/starbugs/linux-%E6%96%B0%E6%89%8B%E5%85%A5%E9%96%80%E5%BF%85%E8%A3%9D-tldr-%E5%91%BD%E4%BB%A4%E5%88%97%E7%A5%9E%E5%85%B5-9fbba0d4f028
[Command Line - Terminal、基本指令介紹 | Yakim shu](https://yakimhsu.com/project/project_w1_CommandLine.html)
[Mac OS X Terminal 終端機常用語法教學 | 梅問題．教學網](https://www.minwt.com/mac/14653.html)
