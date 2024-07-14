---
layout: post
related_posts:
  - /study/state/2024-01-21-react query에 대해서.markdown
title: react query 적용 해보기
categories:
  - study
  - state
image: /assets/img/state/react query.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  Next.JS App router 환경에서 react query 적용 해보기
---

* toc
{:toc}

Next.JS App router 환경에서 react query를 적용하는 방법과 내용을 정리해보겠습니다.

# react query

react query는 react 애플리케이션에서 서버 상태 가져오기, 캐싱, 동기화 및 업데이트를 매우 쉽게 만들어줍니다.

- - -

# 설치하기

react query 라이브러리는 Tanstack으로 변경되었다. 그래서 `@tanstack/react-query`로 설치해야됩니다.

```shell
npm i @tanstack/react-query
# or
yarn add @tanstack/react-query
```

- - -

# 적용하기
Next.js에 App Router를 사용하기 때문에 기존에 사용하던 방법과 다른 방법을 적용했습니다.

`utils`폴더안에 `reqctQueryProvider.tsx`파일을 하나 만들었습니다.

```jsx
'use client';

import { QueryClientProvider, QueryClient } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';
import { useState } from 'react';

export default function ReactQueryProvider({
  children,
}: React.PropsWithChildren) {
  const [client] = useState(
    new QueryClient({
      defaultOptions: {
        queries: {
          refetchOnWindowFocus: false,
          refetchOnMount: false, 
          retry: 1, 
        },
      },
    }),
  );

  return (
    <QueryClientProvider client={client}>
      {children}
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  );
}
```

>`queries` 
**refetchOnWindowFocus: false**
윈도우가 다시 포커스되었을때 데이터를 refetch
>
>**refetchOnMount: false**
데이터가 stale 상태이면 컴포넌트가 마운트될 때 refetch
>
>**retry: 1**
API 요청 실패시 재시도 하는 옵션 (설정값 만큼 재시도)

- `use client` 추가
Next.JS App router는 기본적으로 서버 컴포넌트라서 `QueryClientProvider`를 비릇해서 서버 컴포넌트에서는 작동하지 않습니다.

- `const [client] = new QueryClient()`
참조 동일성을 유지하고 react에서 상태를 관리하는데 도움이 되서 `useState`로 생성했습니다.

- `defaultOptions`
서버 비용을 줄이기 위해 refetch 설정을 해줬습니다. 

`queryCLient`를 `QueryClientProvider`에 만들어둔 `client`를 추가해주고 `children`을 작성해줍니다.

## 1) useState로 생성하는 이유?

>여기 블로그에 잘 정리 되어있습니다. 
https://velog.io/@okko8522/Next.JS%EC%97%90-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%BF%BC%EB%A6%AC-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0

`useState`를 활용하는 것은 react query의 `QueryClientProvider` 설정에서 특별한 경우의 선택이라 할 수 있다.
`useState`를 적용하면 참조 동일성을 유지하며, 또한 react query와 react가 예상하는 상태 관리를 조절하고, 컴포넌트 렌더링과 상태 업데이트를 조절하기 위함이다.

- 초기 렌더링 중 설정
react query는 초기 렌더링 동안에 `QueryClient`를 설정하고 사용하기 위한 상태를 필요로 합니다.

- 동적 설정 관리 
`QueryClientProvider`의 설정을 동적으로 변경해야 할 때, `useState`를 활용해 `QueryClient를` 상태로 관리하는 것이 유용합니다.

- 컴포넌트 렌더링과 분리
`QueryClientProvider`의 설정을 컴포넌트 수명주기와 연관해서 관리하고자 할 때 `useState`를 사용할 수 있습니다.


## 2) layout.tsx에 적용하기
Next.JS Page router는 `_app.tsx`에 넣으면 되지만 App router는 `layout.tsx`에 적용시키면 됩니다.

```jsx
import ReactQueryProvider from '@/utils/reactQueryProvider';

export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang='ko-KR'>
      <body>
        <ReactQueryProvider>{children}</ReactQueryProvider>
      </body>
    </html>
  );
}
```


## 3) Devtool
`QueryClientProvider`안에 `ReactQueryDevtools`을 감싸줍니다. 

<img src="/assets/img/state/react query devtool.png" alt="도구" />

그러면 이런 아이콘이 나오고 reqct-query-devtool을 사용할 수 있습니다.

## 1) 설치
```js
npm i -D @tanstack/react-query-devtools
# or
yarn add -D @tanstack/react-query-devtools
```

## 2) 적용

```jsx
<ReactQueryDevtools initialIsOpen={false} />
```

---
# 참고
>https://velog.io/@okko8522/Next.JS%EC%97%90-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%BF%BC%EB%A6%AC-%EC%A0%81%EC%9A%A9%ED%95%98%EA%B8%B0


