---
layout: post
related_posts:
  - _posts/study/react/2023-11-26-React.memo, useMemo, useCallback 이해하기.markdown
title: 무한 스크롤 (Infinite scroll)
categories:
  - study
  - react
image: /assets/img/react/React.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  무한 스크롤 (Infinite scroll)
---

* toc
{:toc}

게시판을 만들었던 프로젝트에서 무한 스크롤 방식을 사용했습니다. 다시 한번 공부도 할겸해서 글을 작성해보았습니다. 많은 양의 데이터를 추가 클릭하지 않고 스크롤해서 보기 편하게 무한 스크롤을 React 환경에서 구현 해봤습니다.

# 무한 스크롤 (Infinite scroll)
사용자가 페이지 하단에 도달했을 때, 콘텐츠가 계속 로드되는 사용자 경험 (UX) 방식입니다.
한 페이지 아래로 스크롤 하면 끝없이 새로운 화면을 보여주게 되고 이로 인해 많은 양의 콘텐츠를 스크롤 해서 볼 수 있습니다

## 장점

**1) 콘텐츠 탐색이 쉬워집니다.**
더 많은 콘텐츠를 버튼으로 클릭하지 않고도 계속 콘텐츠를 볼 수 있는 경험을 줄 수 있습니다.

**2) 더 나은 사용자 경험을 줄 수 있습니다.**
버튼을 누르고 새 페이지가 로드될 때까지 기다리지 않아도 되고 시간 단축의 좋습니다.

**3) 모바일 기기에 적합합니다.**
모바일 기기의 작은 화면을 쉽고 간편하게 사용할 수 있습니다.


## 단점

**1) 특정 콘텐츠를 다시 찾기가 어렵습니다.**
특정 항목의 위치를 기억하고 해당 항목으로 들어가기에 어려운 문제점이 있습니다.

**2) 스크롤 막대가 실제 데이터양을 반영하지 못합니다.**
스크롤 막대가 실제 데이터의 양을 반영하지 못해 전체 데이터의 양을 알지 못합니다.

**3) 푸터 (하단)에 접근하기 어렵습니다.**
푸터의 추가적인 정보나 유용한 정보가 있을 경우 읽지 못하게 될 수 있습니다.

**4) 페이지 속도가 느려집니다.**
콘텐츠가 방대할수록 무한히 많은 콘텐츠를 로드하는데 많은 시간이 걸릴 수 있습니다.

- - -

# 구현

<img src="/assets/img/react/무한 스크롤.gif" alt="무한 스크롤">

저는 **IntersectionObserver** 와 **useRef**, **Hook**을 사용해서 구현을 해보겠습니다.

## 1) Hook
커스텀 훅을 만들어서 클라이언트에 보내줄 데이터를 담아준다.

저 같은 경우 Hook폴더/useWebtoon.ts 라고 생성해주었다.

- **useWebtoon.ts**

```typescript
export default function useWebtoon(
  page: number,
) {
# 로딩을 담당하는 state
const [loading, setLoading] = useState(true);

# 에러를 담앙하는 state
const [error, setError] = useState(false);

# 사용자가 API 데이터의 결과를 담을 state - DataType는 데이터 타입에 맞게 만들어준것이다.
const [data, setData] = useState<DataType[]>([]);

# 불러운 데이터가 있을 경우를 담을 state
const [hasMore, setHasMore] = useState(false); 
  
useEffect(() => {
    setLoading(true);
    setError(false);

    axios({
      method: "GET",
      url: "데이터를 받아올 API 주소",
      params: {
        page: page
      },
    })
      .then((res) => {
        # Data에 담겨있던 데이터와 새로 불러운 데이터를 합쳐준다.
        setData((prev: DataType) => {
          return [
            ...new Set([
              ...prev,
              ...res.data.webtoons.map((webtoon: DataType) => webtoon),
            ]),
          ];
        });
      	# 불러온 데이터의 길이가 0보다 많으면 true
        setHasMore(res.data.webtoons.length > 0);
      	# loading을 false
        setLoading(false);
      })
      .catch((e) => {
      	# 오류가 나는 경우 error에 true
        setError(true);
      });
    return () => cancel();
  }, [page]);
  return { loading, error, data, hasMore };
}
```

- **로딩을 담당하는 state**
const [loading, setLoading] = useState(true);

- **에러를 담앙하는 state**
const [error, setError] = useState(false);

- **사용자가 API 데이터의 결과를 담을 state**
const [data, setData] = useState<`DataType`>([]);

-  **불러운 데이터가 있을 경우를 담을 state**
const [hasMore, setHasMore] = useState(false);

- DataType는 데이터 타입에 맞게 만들어준것이다.

- **res.data.webtoon.length**는 내가 받아온 데이터가 webtoons에 데이터가 담겨있었다. 
  
이렇게 page에 상태변화가 생기면 재렌더링을 해주게 만들어줍니다.

## 2) IntersectionObserver
  
이제 IntersectionObserver을 사용해서 무한 스크롤을 적용시킬 클라언트에 적용시켜준다.
  
> IntersectionObserver란?
>IntersectionObserver 인터페이스는 대상 요소와 그 상위 요소 혹은 최상위 도큐먼트인 viewport와의 교차 영역에 대한 변화를 비동기적으로 감지할 수 있도록 도와줍니다.

arget Element가 화면에 노출되었는지 여부를 간단하게 구독할 수 있는 API입니다.

```tsx
const [page, setPage] = useState<number>(0);
const { data, hasMore, loading, error } = useWebtoon(page);
  
  
 const observer: React.MutableRefObject<IntersectionObserver | null> =
    useRef<IntersectionObserver | null>(null);
 const lastElementRef = useCallback(
    (node: HTMLElement | null) => {
      if (loading) return;
      if (observer.current) observer.current.disconnect(); // 관찰자의 현재점 끊기
      observer.current = new IntersectionObserver(
        (entries) => {
          if (entries[0].isIntersecting && hasMore) {
            setPage((prev: number) => prev + 1);
          }
        },
        { threshold: 1.0 }
      );
      if (node) observer.current.observe(node);
    },
    [loading, hasMore]
  );
```
IntersectionObserver는 Callback 함수를 통해 두개의 매개변수를 받습니다.
  
- entries
  더 보이거나 덜 보이게 되면서 통과한 역치를 나타내는, IntersectionObserverEntry 객체의 배열
 
- observer
  자신을 호출한 IntersectionObserver
  
option로 root / rootMargin / threshold 값을 커스텀 할 수 있다.

- root
  대상이 되는 객체의 가시성을 확인 할 때 사용하는 뷰포트 요소, 기본값은 브라우저 뷰포트

- rootMargin
  root 가 가진 여백, 교차성을 개산하기 전에 적용됨

- threshold 
  observer의 콜백함수가 실행될 대상 요소의 가시성 퍼센테이지
  10%만 보여도 수행하는가?, 100% 다보였을때 실행하는가
  

observer라는 객체를 useRef를 사용해서 하나 생성해줍니다.
그리고 10개의 데이터를 차례대로 가져와서 보여준다고 가정했을 때 마지막 10에 위치한 데이터에 lastElementRef를 만들어줍니다.
이제 이러면 10에 위치한 데이터를 감지하면 setPage((prev: number) => prev + 1)를 실행하여 다음 page를 넘어가게 되고 상태변화를 주어 useWebtoon(page)가 다시 실행하게 됩니다.
  
  
  
```js
data.map((data:Types, index: number) => {
          if (data.length === index + 1) {
            return (
              <div key={index} ref={lastElementRef}>
                <Container data={data}/>
              </div>
            );
          } else {
            return (
              <div key={index}>
                <Container data={data}/>
              </div>
            );
          }
        })
```

이제 조건문을 사용해서 data.length가 화면에 보여준 데이터의 최대 위치와 맞으면 lastElementRef가 감지할 수 있게 만들어줍니다.

이렇게 무한 스크롤을 완성했습니다.
  
- - -

# 참고 자료
https://velog.io/@eunoia/%EB%AC%B4%ED%95%9C-%EC%8A%A4%ED%81%AC%EB%A1%A4Infinite-scroll-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0
<br/>
https://brunch.co.kr/@joohyup1001/50
<br/>
https://tacit.tistory.com/199?category=1011295