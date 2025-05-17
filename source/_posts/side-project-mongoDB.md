---
title: Next.js API 與 MongoDB 設計實作：整合 NextAuth 多元驗證方式（Credentials + OAuth）
date: 2025-05-17 18:23:24
tags: ["Next.js", "Side Project", "MongoDB", "NextAuth"]
categories: Side Project
---

在開發 SaaS 或工具型應用時，「使用者驗證系統」往往是關鍵基礎設施。這篇文章將分享我如何使用 Next.js（App Router）+ MongoDB + NextAuth，同時支援 Google OAuth 和 自定義帳密登入，並分享專案 mongoDB 資料結構與 API 架構。

## MongoDB 資料模型設計

為了支援多元登入方式，我設計了以下幾個集合（Collections）：

1. users - 使用者基本資訊

```json
{
  _id: ObjectId,
  email: String,
  name: String,
  createdAt: Date,
  updatedAt: Date
}

```

2. authProviders - 自定義驗證方式（帳密）

<!-- more -->

```json
{
  userId: ObjectId, // 關聯到 users._id
  type: "credentials",
  passwordHash: String, // bcrypt 加密
  createdAt: Date,
  updatedAt: Date
}
```

3. folders - 使用者資料夾

```json
{
  _id: ObjectId,
  userId: ObjectId,
  name: String,
  description: String,
  createdAt: Date,
  updatedAt: Date
}

```

4. snippets 

```json
{
  _id: ObjectId,
  userId: ObjectId,
  folderId: ObjectId,
  name: String,
  content: String,
  shortcut: String,
  createdAt: Date,
  updatedAt: Date
}

```

## NextAuth 與驗證流程設計

在這個專案中，我將登入驗證分為兩條路徑：

| 驗證方式 | 實作方式 | 資料表使用 |
| -------- | -------- | ---------- |
| Google 登入 | NextAuth OAuth Provider | `accounts` (NextAuth 自動建立) |
| 帳號密碼 | 自定義 API + bcrypt 驗證 | `authProviders` |


## NextAuth 
使用 NextAuth 搭配 Google 登入時，NextAuth 會自動在資料庫中建立並管理一個 accounts collection（資料表）

這是 NextAuth 的內建行為，用來儲存與第三方驗證（如 Google、GitHub 等）相關的資訊。

下面詳細解說 NextAuth 如何使用 MongoDB 中的 `accounts` collection：

### NextAuth 自動建立的 Collections

當使用 NextAuth 與 MongoDB 整合時，NextAuth 會建立 **`accounts`**：儲存第三方驗證資訊

### `accounts` Collection 的結構

當使用者透過 Google 登入時，NextAuth 會在 `accounts` collection 中建立一筆資料，結構如下：

```jsx
{
  "_id": "ObjectId",
  "userId": "ObjectId", // 關聯到 users collection 中的使用者
  "provider": "google", // 驗證提供者，例如 google, github
  "providerAccountId": "google-account-id", // Google 提供的帳號 ID
  "type": "oauth", // 驗證類型
  "access_token": "google-access-token",
  "id_token": "google-id-token",
  "refresh_token": "google-refresh-token",
  "expires_at": 1683500000, // Token 過期時間
  "scope": "email profile",
  "token_type": "Bearer",
  "session_state": "session-state"
}
```

### 與自定義 `authProviders` 的區別

NextAuth 自動建立的 `accounts` collection 與自定義的 `authProviders` collection 有以下區別：

1. **`accounts`**：由 NextAuth 自動管理，專門用於第三方驗證（如 Google、GitHub 等）。
2. **`authProviders`**：由你自定義，專門用於自己實現的驗證方式（如帳號密碼）。

在你目前的登入 API 執行流程中（如 route.ts 檔案所示），你使用 `authProviders` collection 來儲存和驗證使用者的密碼：

```jsx

const authProvider = await db
  .collection("authProviders")
  .findOne({ userId: user._id, type: "credentials" });
if (!authProvider || !authProvider.passwordHash) {
  return NextResponse.json(
    { message: "invalid credentials" },
    { status: 401 }
  );
}
```


### NextAuth 整合設計

 使用 MongoDB Adapter

```ts

// src/app/api/auth/[...nextauth]/route.ts
import { MongoDBAdapter } from "@auth/mongodb-adapter";

const handler = NextAuth({
  adapter: MongoDBAdapter(clientPromise, {
    databaseName: process.env.MONGODB_DB || "snippets-auth",
  }),
  ...
});
```
### 支援多種 Provider
```ts

providers: [
  CredentialsProvider({
    name: "Credentials",
    authorize: async (credentials) => {
      // 自定義帳密驗證邏輯
    },
  }),
  GoogleProvider({
    clientId: process.env.GOOGLE_CLIENT_ID!,
    clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
  }),
]
```

### 自定義 JWT 與 Session 回傳資料
為了讓 API 能拿到使用者資訊，我自定義了 jwt() 與 session() callback：

```ts

// 回傳使用者資訊給 token
async jwt({ token, user, account }) {
  if (account?.provider === 'google' && user) {
    token.id = user.id;
    token.accessToken = account.access_token;
  }
  if (account?.provider === 'credentials' && user) {
    token.id = user.id;
    token.email = user.email;
    token.token = user.token;
  }
  return token;
}

// 將 token 資訊掛載到 session.user
async session({ session, token }) {
  session.user = {
    id: token.id,
    email: token.email,
    token: token.token,
    accessToken: token.accessToken,
    image: typeof token.image === 'string' ? token.image : undefined
  };
  return session;
}
```
這讓我們在前端取得使用者資訊，或是讓 Middleware 驗證身份更方便。


## API 設計架構
所有 API 採用 RESTful 命名方式：

** 認證 API **
POST /api/v1/auth/signup – 註冊帳號

POST /api/v1/auth/login – 登入取得權杖

** 資料管理 API  **
GET/POST /api/v1/folders

GET/PUT/DELETE /api/v1/folders/[id]

GET/POST /api/v1/snippets

GET/PUT/DELETE /api/v1/snippets/[id]

** 健康檢查 **
GET /api/health – MongoDB 狀態回報


## API Middleware 權限驗證

使用 next-auth/jwt 驗證 JWT：

```ts
// src/middleware.ts
export async function middleware(request: NextRequest) {
  // 判斷公開路徑
  const publicPaths = ["/api/v1/auth/login", "/api/v1/auth/signup", "/api/health"];
  if (publicPaths.includes(path)) {
    return NextResponse.next();
  }

  // 驗證權杖
  const token = await getToken({ req: request, secret: process.env.NEXTAUTH_SECRET });
  if (!token) {
    return NextResponse.json({ message: "Unauthorized" }, { status: 401 });
  }

  // 將使用者 ID 附加到請求標頭中
  const res = NextResponse.next();
  res.headers.set("x-user-id", token.id as string);
  return res;
}
```
 確保所有保護的 API 都檢查 x-user-id，實現使用者權限控管。


## 實作涵蓋了完整驗證設計：

- Google OAuth 登入（透過 NextAuth 自動化）
- 自定義帳密登入（使用 bcrypt + MongoDB）
- 多種登入方式共用同一個 users 集合
- API 權限驗證 + Middleware 控制
- 使用 MongoDB Adapter 整合資料庫與 session 管理