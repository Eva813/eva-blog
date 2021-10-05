---
title: 演算法入門
date: 2021-10-02 13:17:40
tags: ["Algorithm"]
---
![](https://i.imgur.com/LHDcmBI.png)

常常看到 YouTube 演算法造成頻道經營的難度、或是facebook演算法而使得行銷曝光度的改變，但始終對演算法這個名詞沒有認識。
藉由 Wilson Ren[課程](https://www.udemy.com/course/algorithm-data-structure/?course_id=3819536&fbclid=IwAR3ugnEJMsniBCvoMHthf31EqEQeG9hCL3BQb2gWS7eAFZ4lcixmWOiwNAo)

## 什麼是演算法？
用以解決問題而可以逐步執行的步驟或程序。
#### 來看看現實生活中的演算法
* Google Map 如何找到最短路徑
* YouTube 推薦給你，認為你有興趣的影片
* FB\IG 的加好友、追蹤推薦

## 演算法比較
有兩個演算法都可以完成目標任務，那我們會如何取決誰比較好？
* 哪個演算法執行速度快？
* 所佔用電腦的記憶體資源少？
### 時間?
首先，在計時演算法所耗時的部分：
* 幫演算法做計時，是不實際的事情
  * 同一台電腦在執行同一任務，所執行的時間會不同
  * 不同電腦、CPU處理速度不一樣
#### 應該考慮，複雜度 Complexity
* 複雜度分為兩種：時間複雜度、空間複雜度 （在本文多是討論時間複雜度）
* 要如何計算時間複雜度?
  * 加、減、乘、除、comparison ，這些每一個都可以被算作一個 operation
  * Complexity: 在所寫的演算法中，總共用到多少 operations(運算子)
  * 使用 function 來顯示 Complexity 和 input size 的關係。

## Big O Notation
1. 是一個工具，用來描述當你的值不斷擴大時，f(n)值會去哪裡
2. 為最壞情況的打算。他會展示一個演算法複雜度的趨勢

### 計算 Big O 的值
1. Constant doesn't matter : 常數它並不重要
  * f(n)=3n ：3為常數、n為變數
2. Small Terms don’t matter
  * fn= 3n^2 + 6n + 4 => 只需保留到fn= 3n^2
3. Logarithm Base doesn’t matter
![](https://i.imgur.com/dNM08rx.png)
## 範例：
1. f(n)=3n
答案：O(n)
2. f(n)=13n^3 + 6n +7 
答案：O(n^3)
3. f(n)=4log₂n
答案：O(logn)
4. f(n)=5
答案：O(1)

## 演算法常見 Big O 的值
由好至差
1. O(1)
2. O(logn)
3. O(n)
4. O(nlogn)
5. O(n^2)
6. O(n^3)
* 很多sorting值會是 O(nlogn)
* 盡量讓演算法可以達到3、4的值
