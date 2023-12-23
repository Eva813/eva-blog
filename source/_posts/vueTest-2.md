---
title: vueTest - Vue test util (1)
date: 2023-01-31 13:45:08
tags: ["test"]
categories: test
---


* 測試 App.vue
	* 引入 `@vue/test-utils` 使用 shallowMount 方法
	* shallowMount 可以渲染出組件
## shallowMount , mount
	shallowMount : 只會渲染該元件當層的資料內容
	mount : 深度渲染，會將元件內所包含的其他元件一起都渲染出
* 例如以下範例: 在 App.vue 中有包含數個元件

```vue
 //App.vue
<template>

	<img class="logo" alt="Vue logo" src="./assets/logo.png" />
	
	<h1>Test demo</h1>
	
	<AddCount />
	
	<HelloWorld />
	
	<div class="itemFlex">
	
	<CardBox v-for="i in 4" :key="i" />
	
	</div>
	
	<UserList />

	<PhotoItem />

</template>
```

* 測試元件

```javaScript
import { shallowMount, mount } from "@vue/test-utils"

import App from '@/App.vue'

  

describe('App.vue test', () => {

	it('測試組件 1', () => {
	
		//渲染出 App 元件
		
		const wrapper = shallowMount(App);
		
		console.log(wrapper.text())
		
		expect(wrapper.text()).toMatch("Test demo")
	
	});

	it('測試組件2', () => {
	
	const wrapper = mount(App);
	
	console.log(wrapper.text())
	
	expect(wrapper.text()).toMatch("若你是寫過 Vue 但沒有寫過單元測試的工程師")
	
	})

})
```

![](https://i.imgur.com/BcvGwlI.png)

get 與 find 差異
* 抓取某個元件中 DOM 元素
* 兩者差異在於
	* 使用 get ，如果找不到元素會報錯，並且直接中斷測試的運行
	* 使用 find 會依據 `exists()` 回傳布林值
	
`<button id="add" class="add-btn" @click="add">add 按鈕</button>`

![](https://i.imgur.com/TWuFWX3.png)

* 測試按鈕存不存在
	* exists : 驗證某元素是否存在
	* [ exists](https://test-utils.vuejs.org/api/#exists)
	
```javaScript
describe('AddCount.vue', () => {

	it('test 1 ', () => {
	
		const wrapper = shallowMount(AddCount);
		
		console.log(wrapper.find('.add-btn'))
		
		expect(wrapper.find('.add-btn').exists()).toBe(true)

	})

  
})
```

![](https://i.imgur.com/0sBL0t6.png)

![](https://i.imgur.com/htkF4X5.png)

### 在情境應用
* 在 AddCount 加入 `isOpen=ref(false)` 來判斷 button 是否在一開始要呈現
* 而再測試文件中就是要測試該按鈕在一開始並不會出現

![](https://i.imgur.com/YkBK6Ry.png)

```javaScript
import { shallowMount } from '@vue/test-utils'

import AddCount from '@/components/AddCount.vue'

  

describe('AddCount.vue', () => {

	it('test 1 ', () => {
	
		const wrapper = shallowMount(AddCount);
		
		console.log(wrapper.find('.add-btn'))
	// 找不到，並且回傳 false
		expect(wrapper.find('.add-btn-err').exists()).toBe(false)
	
	})

  

})
```

## find 與 findAll
以 v-for list 的範例

```vue
<script>

import { ref } from "vue";

import data from "./data.json";

export default {
	
	setup() {
	
		const employeeItem = ref(data);
		
		return {
		
			employeeItem,
		
		};
		
	},

};

</script>

  

<template>

	<ul class="item">
	
		<li class="user_list" v-for="item in employeeItem" :key="item.id">
		
			<div>
			
				<p>員工編號: {{ item.userId }}</p>
				
				<p>姓名: {{ item.username }}</p>
			
			</div>
		
		</li>
	
	</ul>

</template>
```

![](https://i.imgur.com/dqts5eV.png)

![](https://i.imgur.com/PXPy1as.png)




如果是要抓元件中 list 有幾筆
`wrapper.findAll('.user_list')` : 他會是一個陣列資料，其中包含好幾個 DOM wrapper 

![](https://i.imgur.com/I5lNSRX.png)

注意：
	`TypeError: wrapper.findAll(...).text is not a function`
* 搭配使用 `at()` 用於找尋其中某一個物件

```javaScript
import { shallowMount } from '@vue/test-utils'

import UserList from '@/components/UserList.vue'
  

describe('test UserList', () => {

	it('test Dom', () => {
	
		const wrapper = shallowMount(UserList);
	
	// console.log(wrapper.findAll('.user_list').at(0).text())
	
	expect(wrapper.findAll('.user_list').at(0).text()).toMatch('員工編號: 399')
	
	})

})
```

### 另也可以測試所拿到的資料長度
* 測試 DOM 資料長度

```javaScript
import { shallowMount } from '@vue/test-utils'

import UserList from '@/components/UserList.vue'

  
describe('test UserList', () => {

	it('test Dom', () => {
	
	const wrapper = shallowMount(UserList);
	
	// console.log(wrapper.findAll('.user_list').at(0).text())
	
	expect(wrapper.findAll('.user_list').at(0).text()).toMatch('員工編號: 399')
	
	})

	it('test List length', () => {
	
	const wrapper = shallowMount(UserList);
	
	console.log(wrapper.findAll('.user_list').length)
	
	expect(wrapper.findAll('.user_list').length).toBe(6)
	
	})

})
```
