---
title: React - 建立元件
date: 2022-11-22 10:36:50
tags: ["React"]
categories: React
---

![](https://i.imgur.com/VykOAkm.png)

前段時間練習了一些 React 的基礎，對於 JSX 以及一些 hook 的用法比較有概念，所以就以 Bruce 的 React 課程實作再來練習，樣式部分以 Tailwind 為主此外也搭配 TS 來撰寫。

## 1. Header
* 製作 IGHeader 元件，因為到不同頁面都會有 header 存在，所以可以將它放在外面的共用元件。

```tsx
const IGHeader: React.FC = () => {
  const go = useNavigate()
  return (
    <header className="sticky top-0 border-b-[1px] bg-white border-gray-300 ">
      <div className="flex justify-between items-center h-[60px] px-2 lg:max-w-[1024px] lg:mx-auto lg:px-0">
        <img
          className="w-[100px] cursor-pointer"
          src="/images/logo.svg"
        />
        <div className="flex">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            className="h-6 w-6 mr-4 cursor-pointer"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            onClick={() => go('/')}
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth={2}
              d="M3 12l2-2m0 0l7-7 7 7M5 10v10a1 1 0 001 1h3m10-11l2 2m-2-2v10a1 1 0 01-1 1h-3m-6 0a1 1 0 001-1v-4a1 1 0 011-1h2a1 1 0 011 1v4a1 1 0 001 1m-6 0h6"
            />
          </svg>
          <svg
            xmlns="http://www.w3.org/2000/svg"
            className="h-6 w-6 mr-4 cursor-pointer"
            fill="none"
            viewBox="0 0 24 24"
            stroke="currentColor"
            onClick={() => go('/following')}
          >
            <path
              strokeLinecap="round"
              strokeLinejoin="round"
              strokeWidth={2}
              d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z"
            />
          </svg>
          <div className=" h-6 w-6 text-white  bg-gray-900  font-bold flex  rounded-full justify-center items-center">
            E
          </div>
        </div>
      </div>
    </header>
  )
}
```

## 2. IGContainer
頁面佈局設定，除了 限時動態頁面會呈現外，在追蹤者頁面也會使用到
* 使用 Tailwind 設定左右版面以及當螢幕不同尺寸的設定
* 將樣式寫在(style.div 裏面)，作為一個組件 export 出去

## 3. 限時動態列表（IGStory）
 ![](https://i.imgur.com/jzE9sAZ.png)

* 因隸屬於 home 裡面的位置，所以可以將檔案，放在 home 資料夾中，屬於 home 中的組件
//通常在一份專案中不用引入兩個 css 框架
  實作：
  2. List Container 的容器
  [文件](https://tailwindcss.com/docs/overflow#scrolling-horizontally-if-needed)
  * 在組建內部要使用 JS 相關的邏輯的寫法，要在要面用大括號刮起來
  * 每一個小圖都是一個 item ，所以可以先製作組件

### 限時動態 item 組件
* 此組件建立其中的小項目

```javascript
import React from 'react'

type ItemProps = {
  name: string;
  avatar: string;
}

const Item: React.FC<ItemProps> = ({ name, avatar }) => {
  return (
    <div className=" text-center ">
      <div className=" rounded-full w-[56px] h-[56px] ring-2 border-2 border-white ring-red-500 mx-[11px] p-[3px] "
        style={{
          backgroundImage: `url(${avatar})`,
          backgroundPosition: "center",
          backgroundSize: "cover",
        }}>
      </div>
      <p className="text-xs mt-1">{name}</p>
    </div>
  )
}

export default Item
```

### 外層
* 在外層的主要檔案，就是將模板建立好，讓 item 可以 map 進去

```jsx
const IGStory: React.FC = () => {
  const storyData = [
    {
      "id": 1,
      "name": "bruce_fe",
      "avatar": "/images/avatars/a1.png"
    },
    {
      "id": 2,
      "name": "max",
      "avatar": "/images/avatars/a2.png"
    },
    {
      "id": 3,
      "name": "fm",
      "avatar": "/images/avatars/a3.png"
    },
    {
      "id": 4,
      "name": "joanne",
      "avatar": "/images/avatars/a4.png"
    },
    {
      "id": 5,
      "name": "focus",
      "avatar": "/images/avatars/a5.png"
    },
    {
      "id": 6,
      "name": "louis",
      "avatar": "/images/avatars/a6.png"
    },
    {
      "id": 7,
      "name": "alvin",
      "avatar": "/images/avatars/a7.png"
    },
    {
      "id": 8,
      "name": "grace",
      "avatar": "/images/avatars/a8.png"
    },
    {
      "id": 9,
      "name": "rance",
      "avatar": "/images/avatars/a9.png"
    },
    {
      "id": 10,
      "name": "bruce_fe",
      "avatar": "/images/avatars/a10.png"
    }
  ]
  return (
    <div className='w-full flex items-center h-[110px] box-border overflow-x-auto overflow-y-hidden no-scrollbar  shadow-md lg:my-8'>
      {/* <Item name="Mike" avatar='/images/avatars/a1.png' /> */}
      {
        storyData?.map((item) => {
          const { id, name, avatar } = item
          return <Item key={id} name={name} avatar={avatar} />
        })
      }
    </div>
  )
}

export default IGStory
```

## 3. User 組件 
* 使用者資訊表，在不同區塊都有重複出現，所以可以抽成共用組件
* 現定義好 props 的型別
* 依照藥的版型來切出樣貌，將變數帶入

```tsx
type IGUserProps = {
  size?: "medium" | "small";
  showFollow?: boolean;
  isFollowing?: boolean;
  account?: string;
  location?: string;
  avatar?: string;
  id?: number;
}

const IGUser: React.FC<IGUserProps> = ({
  size = "small",
  showFollow = false,
  isFollowing = false,
  account,
  location,
  avatar,
  id,
}) => {
  return (

    <div className="flex h-[70px] items-center box-border px-4 ">
      <div className={`${size === "small" ? "w-[40px] h-[40px]" : "w-[60px] h-[60px]"} overflow-hidden rounded-full `}
        style={{
          backgroundImage: `url(${avatar})`,
          backgroundPosition: "center",
          backgroundSize: "cover",
        }}
      ></div>
      <div className="ml-4">
        <p className="text-sm font-bold">{account}</p>
        <p className="text-gray-400">{location}</p>
      </div>
      {showFollow && (
        <p
          className={`${isFollowing ? "text-gray-700" : "text-blue-400"} text-xs ml-auto cursor-pointer font-bold`}
        > {isFollowing ? "FOLLOWING" : "FOLLOW"}</p>
      )

      }
    </div>
  )
}
```

* memo 對於重複渲染，效能優化使用


[border-box](https://ithelp.ithome.com.tw/articles/10252827)

![](https://i.imgur.com/x16ZQBu.png)


## post 組件
 ![](https://i.imgur.com/HMvp7dy.jpg)

* 分為三個部分
1. user
* 可以使用前面所製作的 user 組件
2. img
* 直接放入該項圖片
3. 評論區
* 也可以製作成小組件，最後再放入 post 組件
* 注意傳入 props 要先設定好傳入的型別

```jsx
import React from 'react'

type CommentsProps = {
  likes: number;
  description: string;
  hashTags: string;
  createTime: string;
  account: string;
}

const Comments: React.FC<CommentsProps> = (
  {
    likes,
    description,
    hashTags,
    createTime,
    account,
  }) => {
  return (
    <div className='px-4'>
      <div className='flex items-center justify-between box-border my-4'>
        <div className='flex py-1'>
          // svg
      </div>
      <p className='text-sm font-bold'>{likes} likes</p>
      <p className='text-sm font-bold'>{description} </p>
      <p className='text-blue-400 font-bold'>{hashTags} </p>
      <p className='text-gray-400 font-[500] text-xs mt-2'>View all 999 comments </p>
      <p className="text-gray-400 text-[10px] mt-1">{createTime}</p>
    </div>
  )
}



```


## profile 區塊
* 實作快速，直接做出簡易的版面
* 套用前面所做的 IGUser 即可以使用

 ![](https://i.imgur.com/0nIA31V.png)

```jsx
import IGUser from "components/IGUser"

const IGProfile = () => {
  const followingList = [
    {
      id: 1,
      location: "Singapore",
      account: "max_999",
      isFollowing: true,
      avatar: "/images/avatars/a1.png",
    },
    {
      id: 2,
      location: "Singapore",
      account: "fm_999",
      isFollowing: true,
      avatar: "/images/avatars/a2.png",
    },
    {
      id: 3,
      location: "Singapore",
      account: "joanne_999",
      isFollowing: true,
      avatar: "/images/avatars/a3.png",
    },
    {
      id: 4,
      location: "Singapore",
      account: "focus_999",
      isFollowing: true,
      avatar: "/images/avatars/a4.png",
    },]

  return (
    <div className="mt-8 ml-6 border-box shadow-md px-4">
      <div>
        <IGUser size='medium' account='Tmommy_0814' avatar="/images/avatar.png" />
      </div>
      <p className="text-gray-400 px-4 mt-2">You are Following</p>
      <div >
        {
          followingList?.map((item) => {
            const { location, account, isFollowing, avatar, id } = item
            return <IGUser location={location} account={account} isFollowing={isFollowing} avatar={avatar} key={id} showFollow />
          })
        }

      </div>
    </div>
  )
}

export default IGProfile

```

## 小結

此次練習在使用 Tailwind 有些不習慣，主要是在於要稍微記得他的關鍵字並去文件查找屬性來應用，待使用到第三的區塊切版時就變得快速些～
另外，使用在課程中 Bruce 也會去說明每個區塊的切割以及重用性，可以將檔案分別放在哪個檔案，以利未來維護。
過去可能拿版面並未想太多，或是沒有多餘時間分析每一頁面的某些區塊是否有重複，我們可以依據建立開關或傳入的資料區紹維變動下版面，就可以達到一起使用 component 的效果。
