---
title: React - 從官方文件學習 Context 的使用
date: 2022-10-18 13:58:40
tags: React
categories: React
---

## Passing Data Deeply with Context
* 當裡面的子狀態，會想要往父層去找尋時，就可以使用Context
*
學習文件：[React Docs](https://beta.reactjs.org/learn?fbclid=IwAR3OXxu3hHQCgDowMfBWzvRDIdPrZpS2-OCktxb_V9EnRYzNrPB_rZ7CrSI)
* 資料會和其他元件要共用
	* prop drilling
 1. 最基本使用
	* 範例：Section.js \ Heading.js
	* children 帶入元件的內容
	
![](https://i.imgur.com/SAaAzP0.png)

2.  將 app.js 改寫

```jsx
import Heading from './Heading.js';
import Section from './Section.js';

export default function Page() {
  return (
    <Section>
      <Heading level={1}>Title</Heading>
      <Section>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Heading level={2}>Heading</Heading>
        <Section>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Heading level={3}>Sub-heading</Heading>
          <Section>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
            <Heading level={4}>Sub-sub-heading</Heading>
          </Section>
        </Section>
      </Section>
    </Section>
  );
}
```


![](https://i.imgur.com/UnXwJxu.png)

以上方式，看到 在 Heading 元件傳入參數
改寫為，在 section 元件就來控制裡面的 Heading 是什麼 level

```jsx
<Section level={3}>  

	<Heading>About</Heading>  
	
	<Heading>Photos</Heading>  
	
	<Heading>Videos</Heading>  

</Section>
```

3. 實作
	1. **Create** a context.
	2. **Use** that context，在子層`import { useContext } from 'react';`
	3. 提供 context 的值

#### Create  a context.

```jsx
import { createContext } from 'react';

export const LevelContext = createContext(1);

```
#### **Use** that context
* 是 Heading 要往外層找資料，所以要在這裡使用 useContext
* 要往外層的地方
* 注意 level 

```jsx
import { useContext } from 'react';
import { LevelContext } from './LevelContext.js';

export default function Heading({ children }) {
  const level = useContext(LevelContext);
  switch (level) {
    case 1:
      return <h1>{children}</h1>;
    case 2:
      return <h2>{children}</h2>;
    case 3:
      return <h3>{children}</h3>;
    case 4:
      return <h4>{children}</h4>;
    case 5:
      return <h5>{children}</h5>;
    case 6:
      return <h6>{children}</h6>;
    default:
      throw Error('Unknown level: ' + level);
  }
}
```

#### Provide the context
* 建立 context 的 provider
* 提供此資料的提供者、元件
* 這個 provider 是放在最外層，所以在此範例應該放在 section 去改寫

##### 原本的 section.js

```jsx
export default function Section({ children }) {
  return (
    <section className="section">
      {children}
    </section>
  );
}

```


##### 改寫section.js
 * level 從這邊拿

```jsx
import { LevelContext } from './LevelContext.js';  

  
export default function Section({ level, children }) {  

return (  

<section className="section">  

	<LevelContext.Provider value={level}>  

		{children}  

	</LevelContext.Provider>  

</section>  

);  

}
```

![](https://i.imgur.com/pI528iX.jpg)


