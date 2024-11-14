---
title: 推送靜態網頁到 GitHub
date: 2020-11-24 14:03:02
tags: GitHub
categories: GitHub
---
## 1.本地端創建資料夾

在自己的user底下，mkdir my_projects
## 2.GitHub上創建專案(repo)

![](https://i.imgur.com/4uIw6uY.png)

* ### 因為本地端的專案沒有下過`git init`，所以選用 ....…or create a new repository on the command line;但如果本地端的資料夾已經 git init過之後，…or push an existing repository from the command line

![](https://i.imgur.com/BINSuXP.png)

## 3.本地端>照著GitHub上的建議複製過去終端機

![](https://i.imgur.com/H1N0l14.png)

## 4.GitHub重新整理，出現專案資料夾


![](https://i.imgur.com/4mZXgOJ.png)

## 5.建立分支，來存放之後上傳的網頁

`git checkout -b gh-pages`：創建新分支(gh-pages)並同時移到此新分支上


`git branch gh-pages`:創建新分支(gh-pages)
`git checkout gh-pages`:移到(gh-pages)分支上


![](https://i.imgur.com/85Z04Hv.png)

## 6.測試檔案上傳

開啟Visual Studio Code內建的終端機(Terminal)快速鍵為Ctrl + ~（鍵盤左上角的那顆，在 Esc 下面），或 功能選單-> View -> Integrated Terminal 。

* 終端機 `git push`
* 第一次push失敗，意指遠端還沒有建立這個branch，所以就依照它的指令操作

![](https://i.imgur.com/rmwYC1e.png)

* 重新整理之後～就會看到本地端的檔案傳上去了

![](https://i.imgur.com/yOEEHtV.png)

* 並且可以查看網址
https://eva813.github.io/my-projects/test-index.html
