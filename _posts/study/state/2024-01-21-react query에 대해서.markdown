---
layout: post
related_posts:
  - /study/state/2024-01-14-react query 적용 해보기.markdown
  - /study/state/2024-02-04-전역상태 상태관리란.markdown
title: react query에 대해서
categories:
  - study
  - state
image: /assets/img/state/react query.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  react query에 대해서
---

* toc
{:toc}

개인 프로젝트에서 리펙토링을 히면서 서버 상태 관리, 캐시 관리 등을 해 줄 있다는 react query를 사용해보며 기록해보겠습니다.

> `react query`는 버전 4가 릴리스 된 상태고, 이름 또한 변경되었습니다.
버전 4이상에서는 `TanStack query`로 변경되었습니다.
그래도 이 글에서는 react query라고 하고 설명하겠습니다.


# react query
react query는 react 애플리케이션에서 서버 상태 관리 가져오기, 캐시, 동기화 및 업데이트를 보다 쉽게 만들어 도와주며 클라이언트 상태와 서버 상태를 명확히 구분하기 위해 만들어진 라이브러리입니다.

## 1) 설치 
```js
npm i @tanstack/react-query
# or
yarn add @tanstack/react-query
```

설치를 해주고 App 컴포넌트 최상단에 `QueryClient`인스턴스를 생성하고 `<QueryClientProvider> </QueryClientProvider>`로 감싸주고 `QueryClientProvider를` 통해 컴포넌트가 `QueryClient` 인스턴스에 접근할 수 있게 해줍니다.

```js
import { QueryClientProvider, QueryClient } from '@tanstack/react-query';

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
        {...}
    </QueryClientProvider>
  );
}

export default App;
```

## 2) 개발자 도구 추가
react query 개발자 도구를 사용하면
- 쿼리 키로 쿼리를 표시
- 개발 중인 모든 쿼리의 상태를 표시
- 쿼리를 볼 수 있는 쿼리 탐색기

등이 있습니다.

### 설치
```js
npm i -D @tanstack/react-query-devtools
# or
yarn add -D @tanstack/react-query-devtools
```

### 적용
```js
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

return (
  <QueryClientProvider client={queryClient}>
  	  {...}
      <ReactQueryDevtools />
  </QueryClientProvider>
    );
```

<img src="/assets/img/state/react query devtool.png" alt="도구"/>

이런 아이콘이 하나 생기면 적용 성공입니다.

- - -

# react query 상태

![[react query state.png]]

`Fresh` : 새롭게 추가된 쿼리 & 만료되지 않은 쿼리 ➜ 컴포넌트가 마운트, 업데이트되어도 데이터 재요청을 하지 않습니다.
`Fetching` : 요청 중인 쿼리
`Stale`: 만료된 쿼리 컴포넌트가 마운트 ➜ 업데이트되면 데이터 재요청
`Inactive` : 비활성화된 쿼리 ➜ 특정 시간이 지나면 가비지 컬렉터에 의해 제거

- - -


# useQuery
**GET**요청과 같은 **생성**작업을 할때 사용되는 Hook입니다.
```js
 import { useQuery } from "@tanstack/react-query";

const response = useQuery({
  queryKey:[],
  queryFn: () => {},
  //...options 
})
```

반환값을 받고 싶으면
```js
 import { useQuery } from "@tanstack/react-query";

const { data } = useQuery({
  queryKey:[],
  queryFn: () => {},
  //...options 
})
```
이렇게 해줍니다.

## 1) queryKey
`querykey` : 쿼리 키를 선언

> 쿼리 키가 다르면 호출하는 API가 같아도 별도로 관리한다.

## 2) queryFn
`queryFn` : Promise를 리턴하는 함수 **(fetch, axios)**

## 3) returns
>더 자세히 알고 싶으면 **공식문서**에서 리턴 값들과 옵션들을 정리되어 있습니다.
https://tanstack.com/query/latest/docs/react/reference/useQuery

제가 잘 사용한 것들 위주로 보면은 

`data` : 서버 요청에 대한 데이터
`isLoading` : 캐시가 없는 상태에서의 데이터 요청 중인 상태
`isFeching` : 캐시의 유무 상관없이 데이터 요청 중인 상태
`isFeched` : 데이터를 불러오는 동안에 데이터가 이미 로드 되어있는 상태
`isError` : 서버 요청 실패에 대한 상태
`error` : 서버 요청 실패 에러 메시지

등이 있습니다.

<img src="/assets/img/state/react query 프로퍼티.png" alt="프로퍼티" />

## 4) options
이 구간에는 gcTime, staleTime 등의 옵션들을 줄 수 있습니다.

`gcTime` : 언마운트된 후 어느 시점까지 메모리에 데이터를 저장하여 캐싱할 것인지를 결정합니다.
>기본값 : 5분 (30000)

`staleTime` : 쿼리가 fresh 상태에서 stale 상태로 전환되는 시간입니다.

>기본값 : 0
**fresh** 상태에서는 컴포넌트가 마운트, 업데이트가 되어도 재요청을 보내지 않습니다.
쉽게 변하지 않는 컴포넌트에 `staleTime`을 정해줍니다.

`refetchOnMount` : 컴포넌트 마운트시 새로운 데이터를 패칭해줍니다.
>기본값 : true
false로 설정할 경우 마운트시 새로운 데이터를 가져오지 않습니다.

`refetchOnWindowFocus` : 브라우저 클릭 시 새로운 데이터 패칭
> 기본값 : true
flase로 설정한 경우 브라우저가 포커스 되어도 데이터를 가지고 오지 않습니다.

`refetchInterval` : 지정한 시간만큼 데이터 패칭
>기본값 : 0
브라우저에 포커스가 없을 때 실행되지 않는다.

`refetchIntervalInBackground` : 브라우저에 포커스가 없어도 refetchInterva에서 지정한 시간 간격만큼 데이터 패칭

> 기본값 : false

`enabled` : 컴포넌트가 마운트 되어도 데이터 패칭이 일어나지 않는다.

>기본값 : false
useQuery의 반환값 중 refetch를 활용하여 데이터 패칭을 할 수 있습니다.
조건으로 아이디가 있으면 true 없으면 false로 설정이 가능합니다.

- - -
# useMutaion
**POST, PUT, DELETE**와 같은 **변경** 및 **수정**작업을 할때 사용되는 Hook입니다.

```js
 import { useMutaion } from "@tanstack/react-query";

const response = useMutaion({
  mutationFn: () => {},
  //...options
});
```

반환값을 받고 싶으면
```js
 import { useMutaion } from "@tanstack/react-query";

const { mutate } = useMutaion({
  mutationFn: () => {},
  //...callback
});
```
이렇게 해줍니다.

## 1) mutationFn
`mutationFn` : Promise를 리턴하는 함수 **(fetch, axios)**


## 2) Invalidation
데이터를 수동적으로 Fetch를 해줘야 한다면 **쿼리 무효화(Invalidation)**를 해주면 해결됩니다.

```js
import { useMutation, useQueryClient } from '@tanstack/react-query';

const queryClient = useQueryClient();


const { mutate } = useMutaion({
  mutationFn: () => {},
  onSuccess: () => {
    // 캐시가 있는 모든 쿼리 무효화
    queryClient.invalidateQueries();
    // queryKey가 'key'로 시작하는 모든 쿼리 무효화
  	queryClient.invalidateQueries({ queryKey: ['key'] })
    }
});
```

## 3) Callback 함수들
`onSuccess` : 성공 여부 확인 후 함수 실행
`onError` : 에러 여부 확인 후 함수 실행

- - -
# 참고
>https://velog.io/@jkl1545/React-Query
https://velog.io/@leemember/React-query-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-useInfiniteQuery-%EB%A1%9C-%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0-react-infinite-scroller


>한번 보면 좋은 곳
https://github.com/ssi02014/react-query-tutorial