---
title: Vue.js 3 - toRef , toRefs
date: 2022-02-28 14:16:15
tags: ["Vue","v-for"] 
---

## toRef
* 將...變成 ref
從 reactive 物件抽出包裝成一個 ref 的值，與原本物件 是連接的，所以修改時，原本的值也會跟著變動。
* 只能處理一個屬性，須告知要輸入的屬性\物件
* 注意: 更改原始物件內的值，所響應會跟著變動

### 在 prop 的應用
* 來自的 props 是 物件資料，可以使用toRef
```javascript
props: {
 apiUser: {
   required: true,
   type: Object
 }
},
setup(props) {
   const userCopy = toRef(props, 'apiUser')
}
```
* 範例二

```html
<template>
    
<h2>姓名: {{person.name}}</h2>
<h2>年齡: {{person.age}}</h2>
<h2>職業描述: {{person.job.front.dec}}</h2>
<button @click='person.name += ~'>修改姓名</button>
<button @click='person.age ++ '>增加年齡</button>
    
</template>

```
```javascript
<script>
import {reactive} from 'vue';

export default {
    name: 'Demo',
    setup(){
        const person = reactive({
            name: 'Eva',
            age: 25,
            job:{
               front:{
                   dec:'前端工程師'
               }
            }
        })
        
        return{
            person
        }
    }
}
</script>
```
* 要將以上的模板寫的更容易

```html
<template>
    
<h2>姓名: {{name}}</h2>
<h2>年齡: {{age}}</h2>
<h2>職業描述: {{dec}}</h2>
<button @click='name += ~'>修改姓名</button>
<button @click='age ++ '>增加年齡</button>
    
</template>

```

```javascript
//就是返回普通的物件內容
        return{
            name: person.name,
            age: person.age,
            dec: person.job.front.dec
        }
```
* 但此方式要注意，字串模板會無法響應更新的問題

* 使用 toRef 來達到響應

```javascript
<script>
import {reactive,toRef} from 'vue';

export default {
    name: 'Demo',
    setup(){
        const person = reactive({
            name: 'Eva',
            age: 25,
            job:{
               front:{
                   dec:'前端工程師'
               }
            }
        })
        
        //單一轉換
        const name2 = toRefs(person,'name')
        
        return{
            name: toRef(person,'name'),
            age: toRef(person,'age'),
            dec: toRef(person.job.front,'dec')
        }
    }
}
</script>
```


## toRefs
toRefs用來把響應式物件轉換成普通物件，把物件中的每一個屬性，包裹成ref物件
	* toRefs就是toRef的升級版，只是toRefs是把響應式物件進行轉換

### 延續上方，toRefs

* 可以批量處理，一個物件裡的屬性
    * 只會將第一層展開，所以以下在 job 的內層 

```html
<template>
    
<h2>姓名: {{name}}</h2>
<h2>年齡: {{age}}</h2>
<h2>職業描述: {{job.front.dec}}</h2>
<button @click='name += ~'>修改姓名</button>
<button @click='age ++ '>增加年齡</button>
    
</template>

```

```javascript
<script>
import {reactive,toRefs} from 'vue';

export default {
    name: 'Demo',
    setup(){
        const person = reactive({
            name: 'Eva',
            age: 25,
            job:{
               front:{
                   dec:'前端工程師'
               }
            }
        })
        
        //可以批量處理，所以只需要傳入個物件，
        const new = toRefs(person)
        console.log(new)
        
        //在物件中再放toRefs(person) ，會變成物件包物件，所以要展開
        return{
           ...toRefs(person)
            // name: toRef(person,'name'),
            // age: toRef(person,'age'),
            // dec: toRef(person.job.front,'dec')
        }
    }
}
</script>
```
