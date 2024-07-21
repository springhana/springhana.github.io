---
layout: post
related_posts:
  - _posts/study/react/2024-07-07-useState 이해하기.markdown
title: useEffect 이해하기
categories:
  - study
  - react
image: /assets/img/react/React.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  useEffect 이해하기
---

* toc
{:toc}

`useEffect`는 리액트 컴포넌트가 렌더링 될 때마다 특정 작업을 수행할 수 있도록 하는 훅입니다.

# useEffect 구조

```jsx
useEffect(effect, dependencies?)
```

- `effect` : 첫 번째 인수로는 실행할 부수 효과가 포함된 함수 

- `dependencies` : 의존성 배열
	- 의존성 배열은 어느 정도 길이를 가진 배열일 수도, 아무런 값이 없는 빈 배열일 수도 있고, 배열 자체를 넣지 않고 생략할 수 있습니다.

의존성 배열`dependencies`이 변경될 때마다 첫 번째 인수`setup`가 실행합니다.

다음 `count`를 증가 시키고 콘솔을 확인하는 컴포넌트가 있습니다.

```jsx
import { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log(count)
  }, [count])

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        증가
      </button>
    </div>
  )
}
```

버튼을 클릭하면 `count`가 증가되고 의존성에 추가해준 `count`덕분에 `useEffect`의 첫 번째 인수인 콜백 함수가 실행됩니다.

---
# useEffect는 언제?

## 언제 실행될까?

1. `useEffect`는 처음 렌더링때 DOM을 업데이트한 후, 실행이 됩니다.
2. 의존성 배열에 담긴 값이 변경이 되면 업데이트를 합니다.

```jsx
import { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("count2", count)
  }, [count])

  console.log("count1", count)

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        증가
      </button>
    </div>
  )
}
```

1. 처음 렌더링이 되면 `"count1"`가 먼저 콘솔에 나타나고 그 다음 `"count2"`가 콘솔에 나타납니다.
2. 그 후에 버튼을 누르고 `count`가 업데이트 될때마다 `"count2"`가 콘솔에 나타납니다.

## 언제 변경된 것을 알고 실행할까?

> 모던 리액트 Deep Dive 책을 참고했습니다.

함수형 컴포넌트는 매번 함수를 실행해 렌더링을 수행합니다.

```jsx
function Counter() {
  const [counter, setCounter] = useState(0)

  function handleClick() {
    setCounter((prev) => prev + 1)
  }

  return (
    <>
      <h1>{counter}</h1>
      <button onClick={handleClick}>+</button>
    </>
  )
}
```

버튼을 클릭하면 `counter`에 값을 1씩 증가하는 컴포넌트입니다.

버튼을 클릭하면 함수형 컴포넌트는 `useState`의 원리에 따라 다음과 같이 작동한다고 볼 수 있습니다.

```jsx
function Counter() {
  const counter = 1

  //...
  return (
    <>
      <h1>{counter}</h1>
      <button onClick={handleClick}>+</button>
    </>
  )
}
```

함수형 컴포넌트는 렌더링 시마다 고유의 `state`와 `props` 값을 갖고 있습니다.

`useEffect`가 추가된다면 다음과 같은 형태로 볼 수 있습니다.

```jsx
function Counter() {
  const counter = 1

  useEffect(() => {
    console.log(counter)
  })

  //...
  return (
    <>
      <h1>{counter}</h1>
      <button onClick={handleClick}>+</button>
    </>
  )
}
```

`useEffect`는 렌더링할 때마다 의존성에 있는 값을 보면서 이 의존성의 값이 이전과 다른 게 하나라도 있으면 부수 효과를 실행하는 평범한 함수라 볼 수 있습니다.

- `useEffect`는 `state`와 `props`의 변화 속에서 일어나는 렌더링 과정에서 실행되는 부수 효과 함수라고 볼 수 있습니다.

> 부수 효과란 Side Effect라고 부르며 함수 내에서 어떤 구현이 함수 외부에 영향을 끼치는 경우 해당 함수는 Side Effect가 있다고 말합니다. 즉 "의도하지 않은 결과"을 의미합니다.


## 언제 사용하면 될까?

**리액트가 DOM을 업데이트한 후, 추가로 코드를 실행해야 하는 경우가 있습니다.**

예를 들어, 사용자의 입력을 받아 상태를 업데이트하는 경우 입력 값이 변경될 때만 API 호출하고 싶을 때 `useEffect`를 사용하면 됩니다.

```jsx
useEffect(() => {
  const fetchData = async () => {
    const response = await fetch('API_URL');
    const data = await response.json();
    setState(data);
  };
  fetchData();
}, [inputValue]);
```

`useEffect`의 첫 번째 인자로 API 호출할 수 있는 함수를 넣어주고, 두 번째 인자로 의존성 배열에 사용자의 입력 `inputValue`을 넣어주면 됩니다.

리액트가 `useEffect`내에 넘긴 함수를 기억했다가 DOM 업데이트한 후, `fetchData`함수를 실행했습니다.

---
# useEffect 클린업 함수

클린업 함수(clean-up)는 `useEffect`에서는 첫 번째 인수인 콜백 함수에서 `return`되는 함수입니다.

**컴포넌트가 사라질 때(unmount 비슷 시점), 특정 값이 변경되기 직전(count update 직전)에 실행할 작업을 지정할 수 있습니다.**

```jsx
useEffect(() => {
  // mount 시점, count이 update 시점에 실행할 작업 (componentsDidMount)
  return () => {
    // unmount 비슷 시점, count이 update 직전에 실행할 작업(componentWillUnmount)
  }
}, [count])
```

⭐ 하지만 클린업 함수는 `unmount` 개념과는 조금 차이가 있습니다. 

`unmount`는 특정 컴포넌트가 DOM에서 사라진다는 것을 의미하는 클래스형 컴포넌트의 용어입니다. 클린업 함수는 `unmount`라기보다는 함수형 컴포넌트가 리렌더링됐을 때 의존성 변화가 있었을 당시 이전의 값을 기준으로 실행됩니다. (이전 상태를 청소해 주는 개념)

그럼 `count`를 증가 시키는 컴포넌트를 예제로 보겠습니다.

```jsx
import { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log("count2", count)

    return () => {
      console.log("count1", count)
    }
  }, [count])

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        증가
      </button>
    </div>
  )
}
```

여기서 버튼을 클릭하게 되면  `"count1"`이 먼저 실행이 되고 그 다음 `"count2"`가 실행이 됩니다.

## useEffect로 이벤트 등록하고 지우기

클린업 함수는 이벤트를 등록하고 지울 때 사용할 때 자주 사용한다.

```jsx
import { useState, useEffect } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    function addMouseEvent() {
      console.log("클린업 함수 실행")  
    }
    
    window.addEventListener('click', addMouseEvent)

    // 클린업 함수
    return () => {
      window.removeEventListener('click', addMouseEvent)
    }
  }, [count])

  return (
    <div>
      <button onClick={() => setCount(count + 1)}>
        증가
      </button>
    </div>
  )
}
```

함수형 컴포넌트의 `useEffect`는 그 콜백이 실행될 때마다 이전의 클린업 함수가 존재한다면 그 클린업 함수를 실행한 뒤에 콜백을 실행합니다.

따라서 이벤트를 추가하기 전에 이전에 등록했던 이벤트 핸들러를 삭제하는 코드를 클린업 함수에 추가하여 특정 이벤트의 핸들러가 무한히 추가되는 것을 방지할 수 있습니다.

---
# useEffect 의존성 배열

의존성 배열에는 다음과 같이 할 수 있습니다.

1. 어느 정도 길이를 가진 배열 (값이 있다는 뜻)
2. 아무런 값이 없는 빈 배열
3. 배열 자체를 넣지 않고 생략

## 어느 정도 길이를 가진 배열

어느 정도 길이를 가진 배열은 값을 가지고 있다는 배열입니다.

```jsx
useEffect(() => {
  //...
}, [props1, props2])
```

`props1`이 변경되거나, `props2`가 변경될 경우에 실행합니다.

## 아무런 값이 없는 빈 배열

```jsx
useEffect(() => {
  //...
}, [])
```

빈 배열로 줄 경우 최초 렌더링 일때만 발생하며 다음부터는 더 이상 실행되지 않습니다.

즉, 리액트에서 DOM이 업데이트 되고 한번만 실행하고 싶을때 사용하면 됩니다.

## 배열 자체를 넣지 않고 생략

```jsx
useEffect(() => {
  //...
})
```

의존성을 비교할 필요 없이 렌더링 될 때마다 실행합니다.

그럼 의존성 배열이 없는 `useEffect`가 매 렌더링마다 실행된다면 `useEffect` 없이 써도 될까?

다음 두 코드의 차이점은 다음과 같습니다.

```jsx
function Component1() {
  console.log('렌더링')
}
```

1. 서버 사이드 렌더링 관점에서 `window` 객체의 접근에 의존하는 코드를 사용할 수 없습니다.
2. 즉시 실행은 컴포넌트가 렌더링되는 도중에 실행됩니다.

```jsx
function Component2() {
  useEffect(() => {
    console.log('렌더링')
  })
}
```

1. 서버 사이드 렌더링 관점에서 `window` 객체의 접근에 의존하는 코드를 사용해도 됩니다.
2. `useEffect`는 컴포넌트 렌더링이 완료된 이후에 실행됩니다. 
	- 서버 사이드 렌더의 경우에 서버에서도 실행됩니다.
	- 함수형 컴포넌트의 반환을 지연시키는 행위이기에 무거운 작업일 경우 렌더링을 방해하므로 성능에 악영향을 미칠 수 있습니다.

---
# useEffect의 주의할 점

`useEffect`를 사용할 때 무한 루프에 빠지지 않도록 조심해야 합니다. 

이를 방지하기 위해, 의존성 배열을 올바르게 관리해야 합니다. 

`useEffect` 내부에서 상태를 변경할 때는 해당 상태가 의존성 배열에 포함되어 있는지 확인해야 합니다. 그렇지 않으면, 상태 변경에 의해 컴포넌트가 다시 렌더링되고, 이는 또 다른 `useEffect` 호출을 야기할 수 있습니다.

---
# 참고

> <a href="https://next-block.tistory.com/entry/Side-Effect-%EB%B6%80%EC%88%98%ED%9A%A8%EA%B3%BC">Side Effect (부수 효과)와 Pure Function(순수 함수)</a>
>
> <a href="https://f-lab.kr/insight/understanding-useeffect-in-react">useEffect 이해하기</a>
> 
> <a href="https://sambalim.tistory.com/152">useEffect 이해하기</a>
> 
> <a href="https://lucky516.tistory.com/210">useEffect 이해하기</a>
> 
> <a href="https://ko.react.dev/reference/react/useEffect#removing-unnecessary-function-dependencies">React 공식 문서</a>
> 
> <a href="https://velog.io/@sjoleee_/useEffect%EC%9D%98-cleanup%ED%95%A8%EC%88%98"> isOpen값 false로 변경하기</a>
> 
> 모던 리액트 Deep Dive - 리액트 훅 깊게 살펴보기