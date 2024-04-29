---
layout: post
related_posts:
  - /study/typescript/
title: RN에서 Axios Network Error 오류
categories:
  - study
  - react-native
image: /assets/img/react-native/React Native.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  React Native에서 Axios Network Error 오류
---

* toc
{:toc}

React Native의 Window에서 Android를 개발하고 있었는데 AxiosError가 떴다.

<img src="/assets/img/react-native/axiosError.png" />

강의를 보면서 했지만 IOS에서는 되는거 같은데 유독 Android에서만 오류가 뜬 것 같았다.

그래서 밤새 구글 검색도 해보고 유튜브도 찾아보면서 에러에 대해서 찾아봤다.

# 서버 주소 변경

서버 호출 API를 localhost로 설정했을 경우에는 간혹 안되는 문제가 있는 것 같았다.

`http://localhost:포트번호` 대신에 현재 PC가 쓰는 실제 IP 주소로 설정했다.

```null
// 변경 전
http://localhost:포트번호

// 변경 후
http://IP 주소:포트번호
```

```ts
Platform.OS === 'android' 
? 'http://10.0.2.2:포트번호' // 실제 IP 주소를 넣어도 된다.
: 'http://localhost:포트번호'
```

또는 IP 주소 대신에 `10.0.2.2`를 사용해도 된다.

왜 `10.0.2.2` 주소인지 궁금하다.

# 네트워크 주소 공간

> <a href="https://developer.android.com/studio/run/emulator-networking?hl=ko&_gl=1*14yb8x6*_up*MQ..*_ga*NjcwNTAzNDY2LjE3MTQyMzMwMzQ.*_ga_6HH9YJMN9M*MTcxNDIzMzAzNC4xLjAuMTcxNDIzMzAzNC4wLjAuMA..">공식 문서</a>에서 더 자세히 있다.

<img src="/assets/img/react-native/네트워크 주소 공간.png" />

개발 머신의 주소 `127.0.0.1`은 에뮬레이터 루프백 인터페이스에 상응한다. 개발 머신 루프백 인터페이스에서 실행 중인 서비스에 엑세스하려면 특수 주소 `10.0.2.2`를 대신 사용해야 한다.

- `10.0.2.2` IP 주소는 개발 서버의 `127.0.0.1` 이라고 한다.

---

# 참고

> <a href="https://steemit.com/upvu/@happyberrysboy/expo-react-native-axios-network">[Expo]React Native 에서 Axios Network 에러</a>
> 
> <a href="https://developer.android.com/studio/run/emulator-networking?hl=ko&_gl=1*14yb8x6*_up*MQ..*_ga*NjcwNTAzNDY2LjE3MTQyMzMwMzQ.*_ga_6HH9YJMN9M*MTcxNDIzMzAzNC4xLjAuMTcxNDIzMzAzNC4wLjAuMA..">공식 문서</a>