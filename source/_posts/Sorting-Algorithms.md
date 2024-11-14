---
title: 演算法入門 - Sorting-Algorithms
date: 2021-10-08 14:08:05
tags: ["Algorithm"]
categories: Algorithm
---
![](https://i.imgur.com/glzKE9I.png)

延續 Wilson Ren[課程](https://www.udemy.com/course/algorithm-data-structure/?course_id=3819536&fbclid=IwAR3ugnEJMsniBCvoMHthf31EqEQeG9hCL3BQb2gWS7eAFZ4lcixmWOiwNAo)來認識常見的排列方法！

## 排列的演算法介紹
![](https://i.imgur.com/zeRjPef.png)
* 在JS中，就有內建 `array.sort()`
    * 同樣在其他程式語言，都會有內建的 sorting function
    * 但還是需要知道他們是如何運作
* 總共有 6 種 sorting
* 此篇文章會以前面兩種為主


## Bubble Sort
* 冒泡排序
* 會比較相鄰的元素，如果順序不對會互換element
* 是相當簡單的演算法，在現實世界很少拿來使用，最常用在教學
* 而像在python、java 他們內建的排序演算法，都不是用冒泡排序，多是用 quicksort,merge sort（比較複雜、但有效率）
###  範例說明
![](https://i.imgur.com/pDdBK9i.png)
* 比較array的最後兩個數字 => 發現順序不對就對調
![](https://i.imgur.com/ElpC2qh.png)
* 對調之後，再往下兩個元素
    * 發現順序正確，不需更動
![](https://i.imgur.com/mQuFUvU.png)
* 不更動之後，再往下兩個
![](https://i.imgur.com/Qk1T9c3.png)
以此類推...
* 小結：將找到的最小值，推到最左邊

### 要如何做 Bubble Sort
* 先從一個陣列中2個數值比較開始思考
```
for j from A.length-1 to 1;
   if A[j] < A[j-i] ;
   swap A[j] and A[j-i]
```

* 虛擬碼
![](https://i.imgur.com/2VFPI3E.png)
* i => sorted elements
* j => adjacent elements(j是相對i而來)
```javascript
function bubbleSort(arr){
    for(let i=0;i<arr.length-2;i++){
      for(let j=arr.length-1;j>=i+1;j--){
        if (arr[j]<arr[j-1]){
          //swap arr[j] and arr[j-1]
          let temp=arr[j];
          arr[j] = arr[j-1];
          arr[j-1] = temp;
        }
      }
    }
    console.log(arr);
  }

  bubbleSort([4,1,5,2,7]);
```
* 加入隨機的數字，組成新的陣列
```javascript
function bubbleSort(arr) {
  let step = 0;
  for (let i = 0; i <= arr.length - 2; i++) {
    for (let j = arr.length - 1; j >= i + 1; j--) {
      if (arr[j] < arr[j - 1]) {
        // swap arr[j] and arr[j - 1]
        let temp = arr[j];
        arr[j] = arr[j - 1];
        arr[j - 1] = temp;
        step++;
      }
    }
  }
  console.log("It takes " + step + " steps to complete.");
  console.log(arr);
}

let test = [];

for (let i = 0; i < 100; i++) {
  test.push(Math.floor(Math.random() * 100));
}

bubbleSort(test);
```
## Big O of Bubble Sort
![](https://i.imgur.com/NSg6FSC.png)

* 最糟情況下： 由大到小，要排成 由小到大 => 他交換的次數會是 (n-1)+(n-2)+(n-3)+...+(n-n)次
* 最好的情況： 本身的arr就接近 小到大
    * 優化語法
    * 發現沒有任何elements被交換，就可以停止迴圈
```javascript
function bubbleSort(arr){
    for(let i=0;i<arr.length-2;i++){
      let swapping=false;
      for(let j=arr.length-1;j>=i+1;j--){
        if (arr[j]<arr[j-1]){
          //swap arr[j] and arr[j-1]
          let temp=arr[j];
          arr[j] = arr[j-1];
          arr[j-1] = temp;
          swapping=true;
        }
      }
    }
    if (swapping==false){
      break;
    }
  }
```
* 平均情況還是用到 nested for loop
    * O(n^2)

## Insertion Sort

![](https://i.imgur.com/NwAm9cW.png)
* 效率比 bubble sort 好一些
* 理論上， 都是 O(n^2)
* 不斷做插入的動作
###  範例說明
![](https://i.imgur.com/HtfgFYG.png)
* 先認為這條arr長度是1
* 從陣列最前面開始，要將1插入4這個arr上

![](https://i.imgur.com/CrYGinm.png)
* 數字2，要和左邊的數字作比較
![](https://i.imgur.com/lb7mYhc.png)

* 數字3去比較
![](https://i.imgur.com/gqnhQYA.png)

### 虛擬碼

![](https://i.imgur.com/lNSbpjL.png)
```
index 0,1,2,3,4
value 1,2,3,4,0

```
* 檢查這條arr的 index=1
* 設定``key=A[j]``，並將key插入 sorted sequence 
    * j 的前一格為 i
* 確認 i 有沒有大於key => 也就是對於key而言，要不斷地和它左邊的值比較
    * 如果左邊的值比key大，就要讓他們互換

```
index 0,1,2,3,4
value 1,2,3,4 (i),0 (j key)

while i>0 && A[i] > key
  A[i+1] = A[i]
  i -= 1

```

* 語法

```javascript
let unsorted = [14, -4, 17, 6, 22, 1, -5];

insertionSort(unsorted);

function insertionSort(arr) {
  for (let j = 1; j <= arr.length - 1; j++) {
    let key = arr[j];
    i = j - 1;
    while (i >= 0 && arr[i] > key) {
      arr[i + 1] = arr[i];
      i -= 1;
    }
    arr[i + 1] = key;
  }

  console.log(arr);
  return arr;
}
```

![](https://i.imgur.com/E8xWtkL.png)

## Big O
![](https://i.imgur.com/OVF4VRl.png)
