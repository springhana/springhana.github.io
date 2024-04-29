---
layout: post
related_posts:
  - /study/next/2024-02-11-팀 프로젝트 Vercel 배포.markdown
title: Data Fetching
categories:
  - study
  - next
image: /assets/img/next/Next.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  Next.js의 Data Fetching에 대해서  
---

* toc
{:toc}

Next.js 프로젝트를 하면서 이런 오류을 들을 한 번을 만나본적 있을 것이다. (아님 말구) 

<img src="/assets/img/next/getStaticPaths 오류.png" />

위에 오류는 해당 페이지가 동적으로 설정되지 않아 나타낸 에러입니다.

이런 `getStaticPaths`는 Data Fetching이라고 하는데 Data Fetching에 대해서 알아보겠습니다.



# Data Fetching이란?
Next.js에서는 SSR 지원을 위한 몇 가지 데이터 불러오기 전략이 있는데, 이를 Data Fetching이라고 합니다.

Data Fetching으로 다음과 같은 방식으로 수행할 수 있습니다

- CSR : Client Side Rendering
- SSR : Server Side Rendering
- SSG : Static Site Generation

---
# getStaticProps

만약 페이지에서 `getStaticProps` 함수를 내보내면 Next.js는 빌드 시간에 `getStaticProps` 에서 반환된 `props`를 사용하여 이 페이지를 사전 렌더링합니다.

- SSG (정적 사이트) 생성하기 때문에 **정적으로 결정된 페이지**를 보여주고자 할 때 사용하는 함수입니다.

> 렌더링 유형에 관계없이 모든 `props`는 페이지 컴포넌트로 전달되며, 초기 HTML에서 클라이언트 측에서 볼 수 있습니다.
>
>⚠️ 클라이언트에서 사용할 수 없어야 하는 민감한 정보를 `props`에 전달하지 않도록 주의

```tsx
export async function getStaticProps() {
	const res = await fetch('https://api.github.com/repos/vercel/next.js')
    const repo = await res.json()
    return { props: { repo } }
}
 
export default function Page({ repo }) {
  return repo.stargazers_count
}
```

## 1) getStaticProps는 언제 사용해야 하나요??

- 데이터가 빌드 시간에 사용 가능한 경우
   - `ex)` 블로그의 모든 포스트가 빌드 시간에 이미 존재하는 정적 파일로 생성할 경우	
    
- 데이터가 headless CMS가 제공되는 경우
  - `ex)` Contentful이나 Strapi와 같은 headless CMS에서 데이터를 가져와 빌드 시간에 페이지를 사전 렌더링할 경우
    
- SEO를 위해 페이지가 사전 렌더링되어야 하고 매우 빠른 응답이 필요한 경우
   - `ex)` 전자상거래 사이트에서 상품 페이지는 사전 렌더링되어야 하며 사용자에게 매우 빠르게 응답해야 하는 경우

- 데이터가 공개적으로 캐시될 수 있는 경우
   - `ex)` 웹 사이트의 정적 페이지나 블로그 게시물은 사용자마다 다르지 않으며, 모든 사용자에게 동일한 정보를 제공할 경우
   
## 2) getStaticProps는 어디서 사용하나요??

- `getStaticProps`는 페이지에서만 내보낼 수 있습니다. 
   - `_app`, `_document`, `_error`에서는 내보낼 수 없습니다.

- React가 페이지가 렌더링되기 전에 필요한 모든 데이터를 갖고 있어야 하기 때문이다.

- `export getStaticProps`를 독립적인 함수로 사용해야 합니다. 
   - 페이지 컴포넌트 속성으로 `getStaticProps`를 추가하는 것은 작동하지 않습니다.

---
# getStaticPaths

만약 페이지가 동적 경로를 사용하고 `getStaticPaths`를 사용한다면, 정적으로 생성할 경로 목록을 정의해야 합니다.

동적 경로로 사용하는 페이지에서 `getStaticPaths`라는 함수를 내보내면, Next.js는 지정된 모든 경로를 정적으로 사전 렌더링합니다.

- `getStaticPaths`는 `/page/post/[id]`가 접근 가능한 주소를 정의하는 함수입니다.
   - `/page/post/1`, `/page/post/2` ... 같이 파일 하나로 모든 페이지를 생성할 수 있습니다.
   
- `getStaticPaths`는 해당 페이지로 요청이 왔을 때 제공할 `props`를 반환하는 함수입니다.

```jsx
export async function getStaticPaths() {
  const res = await fetch('http://.../posts')
  const repo = await res.json()
  
  const paths = repo.map((repo)=> ({
  	params: { id: repo.id }
  }))
  
  return {
    paths,
    fallback: true, // false or "blocking"
  }
}

export async function getStaticProps({ params }) {
  const res = await fetch(`https://.../posts/${params.id}`)
  const repo = await res.json()
  return { props: { repo } }
}

 
export default function Page({ repo }) {
  return repo.stargazers_count
}
```

> getStaticPaths API 참조는 `getStaticPaths`와 함께 사용할 수 있는 모든 `parameters`와 `props`을 다루고 있습니다.

`fallback`은 paths에서 리턴되지 않은 경로에 대해서 어떻게 처리지를 정합니다.

- `false` : 404를 전달합니다.

- `true` : 404를 전달하지 않고 사용자가 미리 빌드하지 않은 페이지에 접근할 경우, 빌드되기 전까지는 `fallback` 컴포넌트를 보여주고, 빌드가 완료된 이후에 해당 페이지를 보여주는 옵션입니다.

- `blocking` : 별도의 로딩과 같은 처리를 하지 않고, 단순히 빌드가 완료될 때까지 사용자를 기다리게 하는 옵션입니다. 
   - 서버 사이드에서 렌더링할 때까지 대기한 다음, 렌더링이 완료되면 해당 페이지를 제공합니다.
   
## 1) getStaticPaths는 언제 사용해야 하나요??

- 동적 경로를 사용하는 페이지를 정적으로 사전 렌더링하는 경우

- 데이터가 handless CMS, 데이터베이스, 파일 시스템 등에서 가져올 수 있는 경우

- 데이터가 공개적으로 캐시될 수 있는 경우(사용자별로 다르지 않음)

- 페이지가 사전 렌더링되어야 하며 매우 빠른 성능이 필요한 경우

## 2) getStaticPaths는 어디서 사용하나요??

- `getStaticPaths`는 `getStaticProps`와 함께 사용되어야 하며, 페이지의 동적 경로를 정의하는 역할을 합니다.

- `getStaticPaths`는 `getServerSideProps`와 함께 사용할 수 없으며, 서버 측에서 실행되지 않습니다.

- `getStaticPaths`는 동적 경로를 가진 페이지에서만 사용할 수 있으며, 이 때 해당 페이지에서도 `getStaticProps`가 함께 사용되어야 합니다.

- `getStaticPaths`는 페이지 컴포넌트가 아닌 곳에서 보낼 수 없으며, 반드시 독립된 함수로 내보내야 합니다.
   - `_app`, `_document`, `_error`에서는 내보낼 수 없습니다.

---
# getServerSideProps

`getServerSideProps`는 요청 시간에 데이터를 가져와 페이지의 내용을 렌더링하는 데 사용할 수 있는 함수입니다.

- `getServerSideProps`는 **서버에서 실행되는 함수**이며 해당 함수가 무조건 페이지 진입 전에 이 함수를 실행합니다.

- 응답값에 따라 페이지의 루트 컴포넌트에 `props`를 반환할 수도, 혹은 다른 페이지로 리다이렉트시킬 수도 있습니다.

```jsx
export async function getServerSideProps() {
	// Fetch data from external API
    const res = await fetch('https://api.github.com/repos/vercel/next.js')
    const repo: Repo = await res.json()
    return { props: { repo } }
}
 
export default function Page({ repo }) {
  return (
    <main>
      <p>{repo.stargazers_count}</p>
    </main>
  )
}
```

## 1) getServerSideProps는 언제 사용해야 하나요??

- 개인화된 사용자 데이터나 요청 시간에만 알 수 있는 정보에 의존하는 페이지를 렌더링해야 경우
   - 인증 헤더나 지리적 위치와 같은 정보를 가져와야 할 때 이 함수를 사용할 수 없습니다.

## 2) getServerSideProps는 어디서 사용하나요??

- 만약 요청 시간에 데이터를 가져오지 않거나 데이터와 사전 렌더링된 HTML을 캐시하려면, `getStaticProps`를 사용하는 것이 좋습니다.

---
# getInitialProps

`getInitialProps`는 `getStaticProps`나 `getServerSideProps`가 나오기 전에 사용할 수 있었던 유일한 페이지 데이터 불러오기 수단이였습니다. 

- 현재 사용을 권장하지 않습니다.

```jsx
Page.getInitialProps = async (ctx) => {
  const res = await fetch('https://api.github.com/repos/vercel/next.js')
  const json = await res.json()
  return { stars: json.stargazers_count }
}
 
export default function Page({ stars }) {
  return stars
}
```

## 1) getInitialProps은 권장하지 않아요!!

`getInitialProps`는 페이지 초기 로드 시에는 서버 단에서 호출하지만 `next/link`, `next/router`를 통해 페이지 간 이동이 발생하면 해당 api는 클라이언트 단에서 호출한다. 

만약 서버 단에서 수행되어야 하는 로직이 `getInitialProps`에 존재한다면, 클라이언트에서 이것을 호출하는 시점에서는 문제가 발생합니다.

---
# 참고

> <a href="https://nextjs.org/docs/pages/building-your-application/data-fetching">NEXT.JS 공식 문서</a>
>
> <a href="https://igy95.tistory.com/59">[22.04.21] Data Fetching in Next.js</a>
> 
> <a href="https://velog.io/@jwhan/nextjs-getStaticProps-getStaticPaths-getServerSideProps">[Next.js] getStaticProps, getStaticPaths, getServerSideProps 란?</a>

