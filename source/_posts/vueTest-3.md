---
title: vueTest - Vue test util (2)
date: 2023-08-05 12:31:08
tags: ["test"]
categories: test
---

延續上次介紹常用的 Vue test util 語法，今天要再多介紹幾個。

## `.text()` 與 `.html()`

```javaScript
<template>
  <div class="hello">
    <p>
      For a guide and recipes on how to configure / customize this project,<br />
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener">
        vue-cli documentation
      </a>
    </p>
  </div>
</template>
```

使用 `html` 若直接 console 出會將 template 呈現出來

```
html=> <div class="hello">
      <p> For a guide and recipes on how to configure / customize this project,<br> check out the <a href="https://cli.vuejs.org" target="_blank" rel="noopener"> vue-cli documentation </a></p>
    </div>
```

在測試當中，可以直接抓取該斷落的內容進行比對

1. 取得該段的 html
2. 取得該 DOM 元素，並進行 html 的比對

```javaScript
import { shallowMount } from "@vue/test-utils";
import HelloWorld from "./HelloWorld.vue";

describe("HelloWorld.vue", () => {
  it("html & text", () => {
    const wrapper = shallowMount(HelloWorld);
    // console.log("html=>", wrapper.html());
    // console.log("text=>", wrapper.text());
    expect(wrapper.html()).toMatch(
      `<a href="https://cli.vuejs.org" target="_blank" rel="noopener"> vue-cli documentation </a>`);
    console.log("aLink_>", wrapper.find('a').html())
    expect(wrapper.find('a').html()).toMatch(
      `<a href="https://cli.vuejs.org" target="_blank" rel="noopener"> vue-cli documentation </a>`);
  });
});
```

## component 的簡易測試

如課程範例為一個卡片，裡面有一個圖片元件(ImageBox)、內文元件(Content)

![](https://hackmd.io/_uploads/ryWgDUjoh.png)

```javascript
<template>
  <div id="CardBox">
    <ImageBox />
    <Content v-if="isOpenContent" />
  </div>
</template>
```

test goal: 檢查 component 是否有隨狀態而成功渲染與否。

### findComponent

判斷 component 是否有存在
首先要如何抓元件？

1. 因頁面中是掛載組件，所以這邊使用 mount

```javaScript
import { mount } from "@vue/test-utils";
import CardBox from "./index.vue";
import ImageBox from "./ImageBox.vue";
import Content from "./Content.vue";

describe("CardBox.vue", () => {
  it("findComponent function test", () => {
    const wrapper = mount(CardBox);

    expect(wrapper.findComponent(ImageBox).exists()).toBe(true);
    expect(wrapper.findComponent(Content).exists()).toBe(true);
  });
});

```

- findComponent('.class')
- findComponent({name: a}) 組建名稱
- findComponent({ref: 'ref'}) 組件實體：綁在 DOM 上的 ref
- findComponent(component) 取得組件：抓取導入的組件

### findAllComponents

- 此方式是會回傳陣列，要使用 `at()`
- 不支援 ref 查找

```javaScript
expect(wrapper.findAllComponents(ImageBox).at(0).exists()).toBe(true);
```

```
Using find to search for a Component is deprecated and will be removed. Use findComponent instead. The find method will continue to work for finding elements using any valid selector（tag selectors (div, foo, bar), attribute selectors ([foo], [foo="bar"])...）.
```

## 測試 class and attribute

可以使用在狀態的切換或是驗證資料的對錯，可以用 class 存在與否驗證。

### attribute

- 資料在渲染的時候，是否有正確的塞入到 attribute。
- 在做資料的渲然，將拿到的內容塞入 attribute 中，如 a link 的 href, img src

範例題目延續上述使用得內文元件(Content)

```javaScript
<script>
import { ref } from "@vue/reactivity";

export default {
  props: {
    styleColor: {
      type: String,
      default: "red",
    },
  },
  setup(props) {
    return {
      props,
    };
  },
};
</script>

<template>
  <div class="content">
    <h1 :class="['card_title', props.styleColor]">
      Vue  vue-test-utils
    </h1>
    <a href="javascript:;">click</a>
    <p class="card_text">
      Vue Test Utils (VTU) is a set of utility functions aimed to simplify testing Vue.js components. It provides some methods to mount and interact with Vue components in an isolated manner!
    </p>
  </div>
</template>
```

test goal: 測試 `<h1>` 有沒有存在 styleColor

- 使用 `class()` 帶入參數，就會去檢查該 class 是否存在
  測試架構：

1. 先確定該元件有被渲染出來
2. 找到 h1 tag 並查看是否有 styleColor 傳入
3. 元件內的 a link 放入 href

```javaScript
import { shallowMount } from "@vue/test-utils";
import Content from "./Content.vue";

describe("Content.vue", () => {
  it("find component",()=>{
    const wrapper = shallowMount(Content);

    expect(wrapper.find('.card_title').exists()).toBe(true)
  })
  it("check h1 tag have red class", ()=>{
    const wrapper = shallowMount(Content);

    expect(wrapper.find('h1').classes('red')).toBe(true);
  })
  it("button is disabled attr", () => {
    const wrapper = shallowMount(Content);
    console.log('red', wrapper.find('a').attributes("href"))
    expect(wrapper.find('a').attributes("href")).toBe("javascript:;");
  })
});
```

練習方式，聽完講師分享的操作之後，寫下測試目標的結構，就可以嘗試自己撰寫看看！

## 檢查是否可見 isVisible & exists

前面的範例練習，就可以看檔有使用 exists
在 vue template 中，有使用 `v-if`, `v-show` 來處理 div 顯示與否。
`v-show` 會存在於 DOM 上。

```javaScript
import { shallowMount } from "@vue/test-utils";
import PhotoItem from "./index.vue";

describe("PhotoItem.vue", () => {
  it("DOM is v-if or v-show hide", () => {
    const wrapper = shallowMount(PhotoItem);

    // exists 用來查看是否存在
    expect(wrapper.find("#box1").exists()).toBe(false);
    // isVisible 用來判斷DOM是否給 v-show 隱藏起來
    expect(wrapper.find("#box2").isVisible()).toBe(false);
  });

});
```
