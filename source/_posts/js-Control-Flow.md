---
title: JS基本功- 控制流程 Control Flow
date: 2020-12-26 18:42:57
tags: ["JS","switch","迴圈"]
---
## 簡易控制流程的撰寫
```javascript
var str = '只被當一科';
    if (str === 'all pass') {
      console.log('帶你去歐洲玩');
    } else if (str === '只被當一科') {
      console.log('帶你去宜蘭玩');
    } else {
      console.log('禁足');
    }
    //帶你去宜蘭玩

```

## 使用switch來撰寫(太多如果的話可以改用switch)
* 使用break，是用來跳出swich迴圈（換句話說，沒有寫break的話會使程式一直往下跑）
```javascript
 //用switch 改寫 if else
    var key = 'all pass';
    switch (key) {
      case 'all pass':
        console.log('帶你去歐洲玩');
        break;
      case '只被當一科':
        console.log('帶你去宜蘭玩');
        break;

      default:
        console.log('禁足');
        break;
    }
   // 帶你去歐洲玩
```

## 迴圈基本介紹
loop：一直做一樣的事情
無窮迴圈，發生的情形：沒有設終止條件，或是終止條件設錯 （`control+c` 終止它）
* 可在程式最前面，加入debugger 
![](https://i.imgur.com/kpTOWx4.png)


## 1.迴圈for
> 彭彭的課程
![](https://i.imgur.com/JYayJeg.png)

### 用 for 迴圈數綿羊數到一百隻，直到睡著
* `var`後面宣告的變數，可以替換不同字母
```javascript
// for(初始值;終止條件;每次執行的語句)
    for (var i = 0; i < 100; i++) {
      console.log("第" + (i + 1) + "隻綿羊");

    }
    //會印出1~9
    //i++ 意思指 i+=1 ，也是i=i+1
```
### 用 for 迴圈倒著數綿羊
```javascript
for (var k = 100; k > 0; k--) {
      console.log("第" + k + "隻綿羊");
      if (k === 1) {
        console.log('睡著了!');
      }
    }
```
### 用 for 迴圈 數到第 31 隻綿羊就睡著了！ (break)
```javascript
for (var i = 1; i <= 100; i++) {
      console.log("第" + i + "隻綿羊");
      if (i === 31) {
        console.log('睡著了！');
        break;
      }
       if (i === 10) {
        console.log('覺得想睡了！'); //到10不會停止
      }
    }
```
### 用 for 迴圈 跳著數
* #### 奇數 -> 對2取於數為1者
```javascript
for (var i = 1; i <= 10; i++) {
      if (i % 2 === 1) {
        console.log("第" + i + "隻綿羊");
      }

    }
```
* #### 印出1,4,7,10
```javascript
for (var i = 1; i <= 10; i++) {
      if (i % 3 === 1)
        console.log("第" + i + "隻綿羊");

    }
```

### 用 for 迴圈 我只想跳過第四隻綿羊
* 使用`continue`:會跳過一次(跳過設定的條件)，然後繼續下一次迴圈
* 注意語法的順序，如果console.log()在if..continue之前，會變成先讀取所有的i，不會跳過指定位置

```javascript
for (var i = 1; i <= 10; i++) {
      if (i === 4) {
        continue
      }
      console.log("第" + i + "隻綿羊");

    }
```
![](https://i.imgur.com/1lzSCxs.png)


### for與陣列的搭配
* i是依照索引來計算，故應該是要0,1,2,3,4 => <5
    * 最後一個索引值是4(第5個沒有值)
    
![](https://i.imgur.com/HgTnl9r.png)

![](https://i.imgur.com/UHUvq5n.png)




## 2.迴圈while
#### 透過彭彭的課程來認識迴圈while
![](https://i.imgur.com/pJbuHEp.png)

### 迴圈思考方式：
> 彭彭的課程
> ```javascript
> //1+2+...+50 的運算流程 
> var sum=0;
> var n=1;
> while (n<=50){
 > sum=sum+n;
>  n++
> }
> alert(sum);
> ```

{%note info%}
和迴圈相關的變數追蹤(sum,n)

第0次迴圈:先判斷n是否<=50
   sum:0 ,n:1
第1次迴圈：
   sum:1,n:2
第2次迴圈：先判斷n是否<=50 (根據上一個n是2)
   sum:(1+2)=3,n:3
第3次迴圈：
   sum:(3+3)=6,n:4
...
...

第50次迴圈：
   sum:1275,n:51
第51次迴圈：因大於50，故跳離回圈結束
{%endnote%}


### 延續上面的思考，加入continue

     ```javascript
     var x=0;
      for(var i=0;i<100;i++){
      if(1%2= = =0){continue}
      x++
     }
     alert(x)
     ```
{%note info%}
變數追蹤(x,i)
0:
  x:0,i:0
1: 
  x:0 (i為0，對2取餘數為0，進入continue，然後重新開始回圈，沒有進入x++), i:1
2: 
  x:1(i為1，對2取餘數不為0，所以沒有執行continue，直接進入x++),i:2

3:
  x:1,i:3
4:
  x:2,i:4
...
100:
  x:50,i:100
  
{%endnote%}

### 用 while 也可以達到相同的屬羊效果
* 印出0~10
* 相對for囉唆一點

```javascript
  let x = 0;
    while (x < 10) {
      console.log(x);
      x++;
    }
```

![](https://i.imgur.com/dl0l82W.png)


* #### 利用console來debug
```javascript
let x = 0;
    while (x < 10) {
      //利用console來debug
      console.log(x)
      x++;
      console.log("第" + x + "隻綿羊");

    }
```
![](https://i.imgur.com/f347W4t.png)

* #### 搭配continue
```javascript
let x = 0;
    while (x < 10) {
      //利用console來debug
      console.log(x)
      x++;
      if (x == 4)
        continue;
      console.log("第" + x + "隻綿羊");

    }
```

* #### 搭配break
```javascript
let k = 0;
    while (k < 10) {

      k++;
      console.log("第" + k + "隻綿羊");
      if (k == 5)
        break;


    }
```

## 3.迴圈 do..while
* `()`放條件，`{}`放區塊
```javascript
let i = 0;
    do {
      i++
      console.log('第' + i + '隻綿羊')
    } while (i < 10)

```
### 與while的不同
* 因為`i<10`的條件在最後面，所以他會先跑完do，再到while終止
    * i++完之後，跑到`while(11>10)`，跳出迴圈，執行console.log
```javascript
 let i = 10;
    do {
      i++
      console.log('第' + i + '隻綿羊')
    } while (i < = 10);
    console.log('i=',i); //i=11
```
* 另一種寫法
```javascript
let i = 10;
    do {
      i++
      console.log('第' + i + '隻綿羊')
      if (i>10){
         break    //同等於終止條件
      }
      
    } while (true);  //如果沒有if條件，這裡又設true，此狀況下會形成無限迴圈
    console.log('i=',i); //11
```

![](https://i.imgur.com/31WqBwO.png)
* 同樣放入while回圈，結果就不會顯示，因為一開始k=10，進入比較，就不符合條件
```javascript=
let k = 10;
    while (k < 10) {
      k++;
      console.log('第' + k + '隻綿羊')
    }
```

## 雙層回圈
### 用 99 乘法表學雙層迴圈
```javascript
//有兩層，需數2次
    for (i = 1; i < 10; i++) {

      for (k = 1; k < 10; k++) {

        console.log("i:" + i, "k:" + k);
        // console.log(i + '*' + k + '=' + (i * k));
        let result = i * k;
        let str = i + "x" + k + "=" + result;
        console.log(str);
      }

    }
```

---

參考資料：
[MDN continue](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/continue)
[重新認識 JavaScript: Day 09 流程判斷與迴圈](https://ithelp.ithome.com.tw/articles/10191453)
[JS跳出循环的三种方法(break, return, continue)
](https://blog.csdn.net/XXJ19950917/article/details/78310346)
