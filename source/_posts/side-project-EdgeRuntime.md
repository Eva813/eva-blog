---
title: Next.js Middleware 中的身份驗證：Edge Runtime 限制與解決方案
date: 2025-05-17 19:06:42
tags: ["Next.js", "side-project"]
categories: side-project
description: "A project exploring the integration of Edge Runtime with Next.js and MongoDB."
---

最近在做一個 Side Project 時，我想保護部分頁面（像是 /dashboard）需要登入才能訪問，於是決定使用 Next.js 的 Middleware 功能來進行 JWT 身份驗證。

理論上應該很簡單——從 Cookie 抓 token，用 jsonwebtoken 驗證它。但很快我就遇到阻礙：在 Middleware 中用 verify() 解 token，竟然直接報錯，完全無法運行。

## 為什麼 jsonwebtoken 在 Middleware 裡失敗？

當你試圖這樣寫時：

```js

import { NextResponse } from 'next/server';
import { verify } from 'jsonwebtoken';

export function middleware(request) {
  const token = request.cookies.get('token')?.value;

  try {
    const decoded = verify(token, process.env.JWT_SECRET); // ❌ 會報錯
    return NextResponse.next();
  } catch (error) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
}
```

會出現這個錯誤訊息：

```javascript
Error: The Edge Runtime does not support Node.js 'crypto' module.
```

這代表你不能在 Middleware 中使用 Node.js 的某些套件，像是 crypto。


### Edge Runtime 是什麼？
Next.js 的 Middleware 是在 Edge Runtime 上執行的，這個環境接近於瀏覽器，不具備完整的 Node.js API。所以 jsonwebtoken 這種依賴 Node.js crypto 模組的函式庫在這裡無法運作。


## 解法一：使用 jose 函式庫 （採用方式）

jose 是專門設計來兼容 Web Crypto API 的 JWT 函式庫，可以在 Edge Runtime 中順利運作。

### Middleware 驗證範例：

```js
import { NextResponse } from 'next/server';
import { jwtVerify } from 'jose';

export async function middleware(request) {
  const token = request.cookies.get('token')?.value;

  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  try {
    const secret = new TextEncoder().encode(process.env.JWT_SECRET); // Web API 兼容
    const { payload } = await jwtVerify(token, secret);

    // 你可以選擇把 payload 傳給下游使用
    const requestHeaders = new Headers(request.headers);
    requestHeaders.set('x-user-id', payload.sub);

    return NextResponse.next({
      request: {
        headers: requestHeaders,
      },
    });
  } catch (error) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/protected/:path*'],
};
```

- 無需額外 API 呼叫
- jose 是針對 Web 環境優化的函式庫，效能好、兼容性強


## 解法二：將驗證邏輯移至 API Route
如果你已經深度依賴 jsonwebtoken，可以將 JWT 驗證邏輯放到 API Route，由 Middleware 呼叫。

- API Route 處理驗證

```ts
// pages/api/auth/verify.js
import { verify } from 'jsonwebtoken';

export default function handler(req, res) {
  try {
    const token = req.body.token;
    const decoded = verify(token, process.env.JWT_SECRET);
    res.status(200).json({ valid: true, user: decoded });
  } catch (error) {
    res.status(401).json({ valid: false, error: error.message });
  }
}
```

- Middleware 呼叫驗證 API

```ts
import { NextResponse } from 'next/server';

export async function middleware(request) {
  const token = request.cookies.get('token')?.value;

  if (!token) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  const response = await fetch(`${request.nextUrl.origin}/api/auth/verify`, {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ token }),
  });

  const result = await response.json();

  if (!result.valid) {
    return NextResponse.redirect(new URL('/login', request.url));
  }

  return NextResponse.next();
}
```

- 增加一次內部 API 呼叫
- 較影響效能與伺服器負載

## 使用 Web Crypto API

進階使用者可以直接用 Web Crypto API 實作 JWT 驗證（這是 jose 底層的做法），但需要處理 Base64 解碼、簽名驗證、payload 檢查等細節。

```js
import { NextResponse } from 'next/server';

async function verifyJWT(token, secret) {
  // 這裡需要實作 JWT 驗證的各個步驟
  // 1. 解析 header 和 payload
  // 2. 使用 crypto.subtle 驗證簽名
  // 3. 檢查過期時間等
  // 注意：這是簡化的示例，實際實作需要更多程式碼
  
  const parts = token.split('.');
  if (parts.length !== 3) throw new Error('Invalid token format');
  
  // 實作 JWT 驗證邏輯...
  
  return { /* 解碼後的 payload */ };
}

export async function middleware(request) {
  // JWT 驗證邏輯
}


```

小結：
在 Next.js Middleware 中處理身份驗證需要注意 Edge Runtime 的限制。使用 jose 函式庫是最直接的解決方案，它專為 Web 環境設計，能在 Edge Runtime 中正常工作。