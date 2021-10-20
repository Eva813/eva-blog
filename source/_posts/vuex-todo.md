---
title: 實作 Vuex - Todo List
date: 2021-10-20 14:32:01
tags: ["Vuex","Vue"]
---
![](https://i.imgur.com/RPoUnyd.png)

之前的實作練習都沒有機會使用到 Vuex，總是覺得它的資料存取、取得或是管理，對於剛接觸Vue 的我來說還是相對複雜，而且剛開始接觸 Vue 總要先練習下 props \ emit 資料傳遞才行啊～
既然已經會使用基礎的資料傳遞後，那就要近一步實作 Vuex 狀態管理

## 實作功能說明
* 資料的取得來自 JSONPlaceholder
  * 從Vuex來實作資料的取得、刪除、更新
* 針對目前取得的資料進行筆數的篩選
* 區分未完成事項、以及完成事項
* 可透過點擊方塊改變色塊
* 接下來～會製作 3個 component ，將元件放到 App.vue

[成品](https://eva813.github.io/Vuex-Todo/index.html)


#### 開始前別忘了先建立 module
## Vuex 檔案的處理
* 這裡開專案直接有建立 vuex
* 進入 store > index.js

```javascript
import { createStore } from 'vuex';
import todos from './modules/todos';


export default createStore({
  state: {
  },
  mutations: {
  },
  actions: {
  },
  modules: {
    todos,
  }
})
```
## 建立 modules 資料夾
* store > modules > todo.js
    * 從這裡的資料取得並傳遞到 Todos.vue
```javascript
import axios from 'axios';

const state = {};
const getters = {};
const actions = {};
const mutations = {};

export default {
  state,
  getters,
  actions,
  mutations
}

```
#### 接下來要嘗試來串取資料
* 取得假資料
[jsonplaceholder-todo](https://jsonplaceholder.typicode.com/todos)
## 在 todo.js 來製作資料的串接
* 首先state這裡要先建立空陣列，getters 則是建立資料取得的函式
* 在action 來發出請求，取得回應，並傳給 mutations
    * mutations 是改變資料的地方
* 引入axios，使用 Asyc await 使用 get 來串接資料
   * 將串到的資料 commit 到 mutations，改變原本todos資料內容

```javascript
import axios from 'axios';

const state = {
  todos: []
};
const getters = {
  getTodos: (state) => state.todos,
};
const actions = {
  async fetchTodos({ commit }) {
    const res = await axios.get("https://jsonplaceholder.typicode.com/todos");
    // console.log(res.data);
    commit('setTodos', res.data);
  }
};
const mutations = {
  setTodos: (state, todos) => {
    state.todos = todos;
  }
};

export default {
  state,
  getters,
  actions,
  mutations
}

```
![](https://i.imgur.com/4wmzRwy.png)
## 建立 Todos.vue
* 此元件主要是呈現所有的 Todo list
### 取得 vuex 中的資料
* 引入 mapGetters, mapActions
* 將要從 getters 拿到的陣列，放到computed做資料的取得
  * 將該陣列`getTodos` 放到 v-for
* 串接的 action 使用 mapActions，於 methods中執行，而此函式要在`created`階段運行

* 如此一來就可以，從 Vuex 中得到資料

```javascript
<template>
  <h3>Todos</h3>
  <div class="todos">
    <div class="todo" v-for="todo in getTodos" :key="todo.id">
      {{ todo.title }}
    </div>
  </div>
</template>

<script>
import { mapGetters, mapActions } from "vuex";
export default {
  name: "Todos",
  methods:{
    ...mapActions(["fetchTodos"])
  },
  computed: mapGetters(["getTodos"]),
  created() {
    this.fetchTodos();
  },
};
</script>

```
![](https://i.imgur.com/4RKUMtJ.png)

![](https://i.imgur.com/IiWla0A.png)


## 製作加入新 todo 功能
* 建立一個新的 component => AddTodo.vue
* 在 AddTodo 這個元件，製作 輸入框
```javascript
<template>
  <div>
    <h3>Add Todo</h3>
    <div class="add">
      <form>
      <input type="text" placeholder="Add Todo..." v-model="title" />
      <input type="submit" value="Submit" />
      </form>
    </div>
  </div>
</template>
<script>
export default {
  name: "AddTodo",
};
</script>
```
### 到 module > todo.js
* 使用 async 與 axios 傳入資料
    * 第二個參數為 title
    * post 的網址一樣，後面接的參數為title, complete: false (any new todo that it's not going to completed)

```javascript
const actions = {
  async fetchTodos({ commit }) {
    const res = await axios.get("https://jsonplaceholder.typicode.com/todos");
    // console.log(res.data);
    commit('setTodos', res.data);
  },
  async addTodo({ commit }, title) {
    const res = await axios.post("https://jsonplaceholder.typicode.com/todos", { title, completed: false });
    commit('addNewTodo', res.data);
  }

};
```

* 而在action 中使用axios 來新增資料，要將這必新資料 push 到 todo的陣列中
    * 存入的該資料要放在，陣列的最前面所以使用 unshift
```javascript
const mutations = {
  setTodos: (state, todos) => {
    state.todos = todos;
  },
  addNewTodo: (state, todo) => {
    state.todos.unshift(todo);
  }
};
```

### 到 AddTodo.vue 來呼叫此 action
* import `mapActions`
    
```html
<template>
  <div>
    <h3>Add Todo</h3>
    <div class="add">
      <form @submit="onSubmit">
        <input type="text" placeholder="Add Todo..." v-model="title" />
        <input type="submit" value="Submit" />
      </form>
    </div>
  </div>
</template>
```
```javascript

<script>
import { mapActions } from "vuex";
export default {
  name: "AddTodo",
  data() {
    return {
      title: "",
    };
  },
  method: {
    ...mapActions(["addTodo"]),
    onSubmit(e) {
      e.preventDefault();
      //點擊後呼叫action中的加入todo函式，並傳入title資料
      this.addTodo(this.title);
    },
  },
};
</script>


```

![](https://i.imgur.com/57j3S5m.png)

### 這裡要特別提出說明
因為是使用線上的虛擬資料，他不允許我們可以存入新資料到他的API裡面
所以可以看到下方的錯誤顯示(重複 id)
而此在重新整理之後，存入的資料會消失

![](https://i.imgur.com/nNh6AXL.png)

![](https://i.imgur.com/0IZlhhL.png)

---

## 製作刪除功能
* 以id作為參數，來依此刪除
* 不用儲存response資料
    * 在 async deleteTodo ，製作 by id 刪除
    * 到 mutations => 使用 filter 存下資料

```javascript
import axios from 'axios';

const state = {
  todos: []
};
const getters = {
  getTodos: (state) => state.todos,
};
const actions = {
  async fetchTodos({ commit }) {
    const res = await axios.get("https://jsonplaceholder.typicode.com/todos");
    // console.log(res.data);
    commit('setTodos', res.data);
  },
  async addTodo({ commit }, title) {
    const res = await axios.post("https://jsonplaceholder.typicode.com/todos", { title, completed: false });
    commit('addNewTodo', res.data);
  },
  async deleteTodo({ commit }, id) {
    await axios.delete(`https://jsonplaceholder.typicode.com/todos/${id}`);
    commit('removeTodo', id)
  }

};
const mutations = {
  setTodos: (state, todos) => {
    state.todos = todos;
  },
  addNewTodo: (state, todo) => {
    state.todos.unshift(todo);
  },
  removeTodo: (state, id) => {
    state.todos = state.todos.filter((todo) => todo.id !== id);
    //使用filter 將，不是id的篩選出來
  }
};

export default {
  state,
  getters,
  actions,
  mutations
}

```
* 回到 Todos.vue
* 在 template 放入垃圾桶的 icon
    * 執行刪除的功能，在 icon 綁定刪除事件`deleteTodo()`
    * `deleteTodo()`就是來自於 methods 取得的事件
```html
<template>
  <h3>Todos</h3>
  <div class="todos">
    <div class="todo" v-for="todo in getTodos" :key="todo.id">
      {{ todo.title }}
      <i class="fas fa-trash-alt" @click="deleteTodo(todo.id)"></i>
    </div>
  </div>
</template>
```
```javascript
<script>
import { mapGetters, mapActions } from "vuex";
export default {
  name: "Todos",
  methods: {
    ...mapActions(["fetchTodos", "deleteTodo"]),
  },
  computed: mapGetters(["getTodos"]),
  created() {
    this.fetchTodos();
  },
};
</script>

```
---
## 製作篩選器功能
* 當 FilterTodo.vue 這裡的篩選器執行的時候，會執行 action中的函式，進行篩選
```html
<style scoped>
select {
  margin-top: 20px;
  padding: 6px;
  border: #ef9a9a 1px solid;
}
</style>
<template>
  <div>
    Filter todos:
    <select>
      <option value="200">200</option>
      <option value="100">100</option>
      <option value="50">50</option>
      <option value="30">30</option>
      <option value="20">20</option>
      <option value="10">10</option>
    </select>
  </div>
</template>
```
```javascript
<script>
export default {
  name: "FilterTodos",
};
</script>
```

將該元件綁到 App.vue
```javascript
<template>
  <div id="app">
    <div class="container">
      <AddTodo />
      <FilterTodos />
      <Todos />
    </div>
  </div>
  <!-- <router-view /> -->
</template>

<script>

import Todos from "@/components/Todos.vue";
import AddTodo from "@/components/AddTodo.vue";
import FilterTodos from "@/components/FilterTodos.vue";

export default {
  name: "app",
  components: {
    Todos,
    AddTodo,
    FilterTodos,
  },
  
};
</script>

```

### 到 module > todo.js 來製作 actions
* 要傳入 option 的 value
    * 第二個參數為事件
    * 在 axios 的 get 放入 url，接上取得的參數
    * commit 直接傳到 setTodos ，將串接到的資料透過 mutations 存到 todos
```javascript
import axios from 'axios';

const state = {
  todos: []
};
const getters = {
  getTodos: (state) => state.todos,
};
const actions = {
  
  async filterTodos({ commit }, e) {
    //取得下拉選單的值
    //console.log(e.target.value);
    let selected = parseInt(e.target.value);
    const res = await axios.get(`https://jsonplaceholder.typicode.com/todos?_limit=${selected}`);
    commit('setTodos', res.data);
  }

};
const mutations = {
  setTodos: (state, todos) => {
    state.todos = todos;
  },
  addNewTodo: (state, todo) => {
    state.todos.unshift(todo);
  },
  removeTodo: (state, id) => {
    state.todos = state.todos.filter((todo) => todo.id !== id);
    //使用filter 將，不是id的篩選出來
  }
};

export default {
  state,
  getters,
  actions,
  mutations
}

```
* 將ａction 綁到 FilterTodos.vue
```javascript

<template>
  <div>
    Filter todos:
    <select @change="filterTodos($event)">
      <option value="200">200</option>
      <option value="100">100</option>
      <option value="50">50</option>
      <option value="30">30</option>
      <option value="20">20</option>
      <option value="10">10</option>
    </select>
  </div>
</template>

<script>
import { mapActions } from "vuex";
export default {
  name: "FilterTodos",
  methods: {
    ...mapActions(["filterTodos"]),
  },
};
</script>

```
---
## todos 完成與否，狀態的改變
* 點擊兩下來更改狀態
    * 當要更新資料，要使用put，傳入updTodo物件資料
        * 將該資料傳到mutation
    * 接著到 mutation 製作更新的動作
    * 要取得目前的index

```javascript
import axios from 'axios';

const state = {
  todos: []
};
const getters = {
  getTodos: (state) => state.todos,
};
const actions = {
  
  async updataTodo({ commit }, updTodo) {
    const res = await axios.put(`https://jsonplaceholder.typicode.com/todos/${updTodo.id}`,updTodo);
    commit('renewTodo', res.data);
  },

};
const mutations = {
 
  renewTodo: (state, updTodo) => {
    console.log(updTodo);
    //我們是要在同一筆資料上更新
    const index = state.todos.findIndex(todo => todo.id === updTodo.id);
    //以下簡單確認 indx 是否有存在
    if (index !== -1) {
      state.todos.splice(index, 1, updTodo);
    }
  }
};

export default {
  state,
  getters,
  actions,
  mutations
}

```
### 回到 Todos.vue
* template 插入legend
* 引入 `updataTodo`到 methods
* `onDblclick()`建立一個變數，它包含的物件內容為id、title、completed，其中我只要要改變的值是 complete true\false
   * 這裡的 id,title 都一樣。complete 則設定狀態的改變
   * 最後在呼叫一次 `updataTodo()`  傳入新的物件
```html
<template>
  <h3>Todos</h3>
  <div class="legend">
    <span>雙重點擊來標示已完成的事項</span>
    <span> <span class="incomplete-box"></span> = 未完成 </span>
    <span> <span class="complete-box"></span> = 已完成 </span>
  </div>
  <div class="todos">
    <div
      @dblclick="onDblclick(todo)"
      class="todo"
      v-for="todo in getTodos"
      :key="todo.id"
      :class="{ 'is-complete': todo.completed }"
    >
      {{ todo.title }}
      <i class="fas fa-trash-alt" @click="deleteTodo(todo.id)"></i>
    </div>
  </div>
</template>

```
```javascript

<script>
import { mapGetters, mapActions } from "vuex";
export default {
  name: "Todos",
  methods: {
    ...mapActions(["fetchTodos", "deleteTodo", "updataTodo"]),
    onDblclick(todo) {
      const updTodo = {
        id: todo.id,
        title: todo.title,
        completed: !todo.completed,
      };
      this.updataTodo(updTodo);
    },
  },
  computed: mapGetters(["getTodos"]),
  created() {
    this.fetchTodos();
  },
};
</script>



```







參考資料：
[[Vue.js] Vuex 學習筆記 (5) - getters 的核心概念](https://ithelp.ithome.com.tw/articles/10191225)
[Vuex](https://vuex.vuejs.org/zh/guide/getters.html#%E9%80%9A%E8%BF%87%E5%B1%9E%E6%80%A7%E8%AE%BF%E9%97%AE)
[Using `this.$store.dispatch` vs using mapActions](https://forum.vuejs.org/t/using-this-store-dispatch-vs-using-mapactions/2186/2)
