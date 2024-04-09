---
layout: post
related_posts:
  - /study/css/2024-03-24-늦었지만 Tailwind CSS 사용하기.markdown
title: Tailwind CSS의 Ascpect Ratio로 사이즈 맟추기
categories:
  - study
  - css
image: /assets/img/css/Tailwind.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  Ascpect Ratio로 사이즈 맟추기
---

* toc
{:toc}

# Aspect Ratio란?

Aspect Ratio는 요소의 종횡비를 제어하는 ​​유틸리티입니다.

<a href="https://tailwindcss.com/docs/aspect-ratio">Tailwind Aspect Ratio</a> 공식문서에서 자세히 나와있습니다.

<img src="/assets/img/css/ascpect ratio.gif">

# 설치

```shell
npm install -D @tailwindcss/aspect-ratio
# or
yarn add -D @tailwindcss/aspect-ratio
```

## 환경설정

`tailwind.config.js`에서 플러그인을 추가해줘야 사용이 가능합니다.

```js
module.exports = {
  corePlugins: {
    aspectRatio: false,
  },
  plugins: [
    require('@tailwindcss/aspect-ratio'),
  ],
}
```

# 사용하기

- `aspect-w-{n}` : 가로 비율
- `aspect-h-{n}` : 세로 비율

```jsx
<div className="aspect-w-16 aspect-h-10">
  <!-- ... -->
</div>
```

- 제거 하고 싶으면 `aspect-none`을 사용하면 됩니다.

```jsx
<div class="aspect-w-16 aspect-h-10 lg:aspect-none">
  <!-- ... -->
</div>
```

---
# 참고

> <a href="https://github.com/tailwindlabs/tailwindcss-aspect-ratio">tailwindcss-aspect-ratio github</a>
>
> <a href="https://enfp-jake.tistory.com/247">[TailwindCSS] Aspect Ratio로 사이즈 정의하기(Feat. 정사각형)</a>