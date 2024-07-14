---
layout: post
related_posts:
  - /study/state/2024-02-04-전역상태 상태관리란.markdown
title: react query의 mutation사용 후 데이터 업데이트하기
categories:
  - study
  - state
image: /assets/img/state/react query.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  react query의 mutation사용 후 데이터 업데이트하기
---

* toc
{:toc}


react query에서 `useMutation`을사용해봤을 것입니다.
 그런데 저 같은 경우는 서버에서 재조회 요청만 사용했기 때문에 잘 사용하지 못했습니다.  그런데 사용 후에 데이터를 업데이트 하는 방법은 재죄회뿐만 아니라 다른 방법들도 존재합니다.

# mutation이란?

**mutation**은 서버에 **사이드 이펙트**를 일으키도록 하는 함수입니다. 인계 받은 쿼리 값을 기반으로 새로운 결과를 도출하여 서버에 변경 사항을 적용하도록 요청하는 기능을 합니다

- **생성(POST), 수정(PUT), 삭제(DELETE)**와 같은 데이터를 변경하는 작업을 할 수 있으며, `useMutation` 훅을 지원합니다.

---
# useMutation 사용법

**mutation** 작업도 `Promise`를 반환하는 비동기 함수를 인자(`mutationFn`)로 넣어야합니다.

```ts
import { useMutation } from "react-query";

const { data, isLoading, mutate } = useMutation({ mutationFn, options });

mutate(variables, {
  onError,
  onSettled,
  onSuccess,
});
```

`useMutation` 훅을 호훌해서 가져온 값 중, `mutate`을 통해 데이터를 수정하도록 요청하는 작업을 수행할 수 있습니다.

옵션으로는 `onError`, `onSettled`, `onSuccess`을 통해 `useMutation` 작업 흐름을 제어할 수 있습니다.

## Options

```ts
useMutation(addTodo, {
   onSuccess: (data, variables, context) => {
     // I will fire first
   },
   onError: (error, variables, context) => {
     // I will fire first
   },
   onSettled: (data, error, variables, context) => {
     // I will fire first
   },
  });

mutate(todo, {
   onSuccess: (data, variables, context) => {
     // I will fire second!
   },
   onError: (error, variables, context) => {
     // I will fire second!
   },
   onSettled: (data, error, variables, context) => {
     // I will fire second!
   },
});
```

- `onError` 

```ts
onError: (error: TError, variables: TVariables, context?: TContext) => Promise<unknown> | void
```

mutation이 에러가 발생할 때 실행됩니다.

- `onSuccess`

```ts
onSuccess: (data: TData, variables: TVariables, context?: TContext) => Promise<unknown> | voi
```

mutation이 성공했을 경우 결과를 전달할 때 실행됩니다.

- `onSettled`

```ts
onSettled: (data: TData, error: TError, variables: TVariables, context?: TContext) => Promise<unknown> | void
```

mutation이 성공을 하거나 실패를 했을 경우에도 그에 맞는 결과가 전달됩니다.

각각 인자의 역할로는 다음과 같다.

`data` : **mutate**함수가 호출되었을 때 인자로 들어간 값이 담깁니다. 

`error` : **mutate**가 에러를 일으켰을 때 반환된 결과를 담깁니다.

`data`: **mutationFn**의 실행된 결과 같이 담깁니다. 
- `onSuccess`, `onSettled`에 사용됩니다.

`context`: `onMutate` 콜백 함수가 반환한 값이 담깁니다. 
- `onError`, `onSettled`에 사용됩니다.

> 이렇게 **mutation**을 사용해주면 되는데 이전의 결과 같은 경우는 업데이트가 되지 않습니다. 그래서 결과를 업데이트를 해줄려면 직접 해줄 수가 있는데 방법은 여러가지가 있습니다.

---
# 결과를 업데이트 하는 방법

1. 서버에서 받아온 데이터로 클라이언트 데이터 업데이트
	- setQueryData
	- invalidateQueries
2. 클라이언트에서 요청 데이터로 적용
	- getQueryData & setQueryData 조합
	- setQueryData & Updater 조합
	- Optimistic Update(onMutate 메서드) 

todoQuery라는 `useQuery`로 받아온 데이터를 기준으로 결과를 업데이트 해보겠습니다.

```ts
const todoQuery = useQuery({
  queryKey: [querykeys.TODO],
  queryFn: fetchTodo,
})
```

## 1. 서버에서 받아온 데이터로 클라이언트 데이터 업데이트

### setQueryData 사용하기

```ts
import { useQuery, useQueryClient } from '@tanstack/react-query'

const queryClient = useQueryClient()

const todoMutate = useMutation({ 
  mutationFn: fetchMutateTodo,
  onSuccess: (data) => {
    queryClient.setQueryData([querykeys.TODO], data)  
  }
})
```

`setQueryData`는 `useQuery`로 조회된 데이터가 캐시에 들어있을 경우, 캐시의 조회 데이터를 업데이트 해줄 수 있는 메서드입니다.

- 이 방법은 반드시 서버에서 **업데이트된 데이터를 반드시 클라이언트에게 수정된 데이터**를 줘야합니다.

### invalidateQueries 사용하기

```ts
import { useQuery, useQueryClient } from '@tanstack/react-query'

const queryClient = useQueryClient()

const todoMutate = useMutation({ 
  mutationFn: fetchMutateTodo,
  onSuccess: () => {
    queryClient.invalidateQueries({ querykey: [querykeys.TODO] })  
  }
})
```

`invalidateQueries`는 `useQuery`로 조회된 데이터가 캐시에 들어있을 경우, 클라이언트에서는 서버에 변경된 데이터를 다시 조회해서 문제를 해결할 수 있습니다.

- 서버에서 데이터를 내려주지 않을 때 유용하게 사용할 수 있습니다.

## 2. 요청 데이터로 클라이언트 데이터 변경

### getQueryData & setQueryData 조합 사용하기

```ts
import { useQuery, useQueryClient } from '@tanstack/react-query'

const queryClient = useQueryClient()

const todoMutate = useMutation({ 
  mutationFn: fetchMutateTodo,
  onSuccess: (data) => {
    const oldData = queryClient.getQueryData([querykeys.TODO]);
    queryClient.setQueryData([querykeys.TODO], {
      return [...oldData, data]
    })  
  }
})
```

`getQueryData`를 통해 변경되기 전 데이터를 캐시에서 조회해줍니다. 그리고 데이터를 워하는 형태로 가공을 해준 이후에 `setQueryData`의 인자로 넣어주면 됩니다. 그러면, 클라이언트의 데이터가 변경되며, query를 선언한 컴포넌트가 리렌더링 되면서 화면이 새롭게 그려집니다.

- 주의점으로 불변성을 지켜서 넣어주어야 합니다.

> 불변성이란?
> 기존의 상태 값을 유지하면서 새로운 값을 추가하는 것을 의미합니다. (값이나 상태를 변경할 수 없는 것을 의미합니다. )

### setQueryData & Updater 조합

```ts
import { useQuery, useQueryClient } from '@tanstack/react-query'

const queryClient = useQueryClient()

const todoMutate = useMutation({ 
  mutationFn: fetchMutateTodo,
  onSuccess: (data) => {
    queryClient.setQueryData([querykeys.TODO], 
    (oldData) => {
      return [...oldData, data]
    })  
  }
})
```

`getQueryData`를 사용하지 않고 `setQueryData`만 사용하는 방법입니다.  

- 캐시에서 다시 조회하는 로직이 없어도 `useMtation` 자체에서 제공하는 방법입니다. 업데이트 함수를 선언하면 알아서 클라이언트 데이터를 변경시켜줍니다.
- `setQueryData`의 두번째 인자로 함수를 넣어주면 됩니다.

### Optimistic Update(onMutate 메서드) 

```ts
const queryClient = useQueryClient()
useMutation({
  mutationFn: updateTodo,
  // mutate 가 호출되었을 때, api 콜 이전에 미리 쿼리 데이터를 수정한다.
  onMutate: async (newTodo) => {
		// mutationFn 이 성공적으로 실행될 것이라 가정하고 현재 쿼리 데이터를 수정했기 때문에
		// 만약 해당 queryKey 에 대해서 다른 refetch 작업이 수행된다면 데이터가 덮어 씌워지므로 이를 방지하기 위함이다.
    await queryClient.cancelQueries({ queryKey: ['todos'] })
		// mutation 작업 이전의 값을 가져와 context 로 이전시킨다.
    const previousTodos = queryClient.getQueryData(['todos'])
	  // 쿼리 데이터를 수정 이후의 값으로 직접, 명시적으로 업데이트 해준다. 
    queryClient.setQueryData(['todos'], (old) => [...old, newTodo])
			// 업데이트 이전의 값이 담긴 context 객체를 return 한다.
    return { previousTodos }
  },
	// 만약 mutate 작업이 실패했을 경우, 이전에 저장했던 값으로 다시 롤백 시킨다.
  onError: (err, newTodo, context) => {
    queryClient.setQueryData(['todos'], context.previousTodos)
  },
	// 실패 혹은 성공 여부와 관계 없이 mutation 작업 이후에는 쿼리를 stale 하게 만들어 refetch를 유도한다. 
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: ['todos'] })
  },
})
```

mutation 작업이 시행되기 이전에 해당 작업이 이미 성공했다고 가정하고, **미리 쿼리에 캐싱된 값에 예상된 결과로 즉시 업데이트**하는 기법입니다.

- 해당 API가 무조건 성공한다는 것을 전체로 수행하기 때문에 만약 실패했을 경우 mutation 작업이 진행되기 전의 값으로 쿼리 데이터를 되돌립니다. (값이 변경되었다가 다시 되돌아가는 과정은 UX 적으로 매우 좋지 않습니다.)

> `onMutate` 콜백 내에서 `cancelQueries` 를 호출하는 이유는, Optimistic Update 을 진행하면서 외부에 refetch 작업이 발생할 경우 값이 재수정 되기 때문입니다.

---
react-query를 재조회만 사용했는데 네트워크 요청을 조금이라도 줄이고 싶으면 직접 캐시를 업데이트 해줘도 좋을 것 같습니다.

# 참고

> <a href="https://velog.io/@rookieand/react-query-%EC%9D%98-mutation%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90">react-query 의 mutation에 대해 알아보자</a>
> 
> <a href="https://pozafly.github.io/react-query/mutation-after-data-update/">React Query에서 mutation 이후 데이터를 업데이트하는 4가지 방법</a>
>
> <a href="https://velog.io/@kimhyo_0218/React-Query-%EB%A6%AC%EC%95%A1%ED%8A%B8-%EC%BF%BC%EB%A6%AC-useMutation-%EA%B8%B0%EB%B3%B8-%ED%8E%B8">[React Query] 리액트 쿼리 useMutation 기본 편</a>