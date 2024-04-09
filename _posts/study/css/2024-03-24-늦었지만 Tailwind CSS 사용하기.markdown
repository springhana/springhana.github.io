---
layout: post
related_posts:
  - /study/css/2024-03-31-Tailwind CSS의 Ascpect Ratio로 사이즈 맟추기.markdown
title: Tailwind CSS
categories:
  - study
  - css
image: /assets/img/css/Tailwind.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  Tailwind CSS 사용하기
---

* toc
{:toc}

# Tailwind CSS란?

Tailwind CSS는 Utility-First를 지향하는 **CSS 프레임워크**입니다.

> Utility-First란?
> 미리 세팅된 유틸리티 클래스를 활용하여 HTML 코드 내에서 스타일링하는 것

CSS의 각 속성들을 클래스에서 직관적으로 표현할 수 있어 효율적으로 사용할 수 있습니다.

---

# 설치하기

Tailwind CSS v2 부터는 IE를 지원하지 않아 PostCSS의  autoprefixer 등을 함께 사용해야 한다고 합니다.

```shell
npm install --save tailwindcss@latest postcss@latest autoprefixer@latest
# or
yarn add tailwindcss@latest postcss@latest autoprefixer@latest
```

## 설정

먼저 플러그인을 만들어 줍니다.

```shell
npx tailwind init -p
```

명령어를 입력해주면 다음과 같이 만들어진 파일들이 보입니다.

<img src="/assets/img/css/설정 파일.png" />

- 만약 tailwind.config.js만 만들어져 있으면 postcss.config.js를 따로 만들어줍니다.

`postcss.config.js`에 다음과 코드가 있는지 확인해주고 없으면 추가해줍니다. (위에 명령어를 했을 경우 자동으로 입력되어 있음)

```js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
  },
}
```

`tailwind.config.js`는 다음과 같이 생성됩니다.

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

- `content`에는 모든 HTML 템플릿, JS 구성 요소 및 Tailwind 클래스 이름이 포함된 기타 파일에 대한 경로를 구성합니다.
	- 저 같은 경우는 `app`, `pgee`, `components` 폴더에서 스타일을 사용했습니다.
```js
  content: [
    "./src/pages/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/components/**/*.{js,ts,jsx,tsx,mdx}",
    "./src/app/**/*.{js,ts,jsx,tsx,mdx}",
  ],
```

- `theme`는 여러 속성에 대해서 커스텀마이징을 할 수 있습니다.
	- 예를 들어 color를커스텀 하고싶으면 다음과 같이 적용하면 됩니다.
```js
  theme: {
    extend: {
      colors: {
        primary: '#FFC700',
        secondary: '#0099DB',
	      },
		}
	}
```

- `plugins`는 재사용 가능한 타사의 플러그인으로 확장합니다.
	- 예를 들어 aspect-adtio를 추가해봤습니다.
```js
  plugins: [require('@tailwindcss/aspect-ratio')],
```

프로젝트 CSS 초기화 파일에 테일원드 지시어를 추가해줍니다.

- 보통 `global.css`나 `index.css`에 추가해줍니다.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

---

# 사용하기

<img src="/assets/img/css/버튼.png" />

이런 버튼을 하나 만들어보겠습니다.

```jsx
<button className="px-5 py-2 bg-primary rounded-md">버튼</button>
```

위에서 설장한 커스텀 색상을 사용해서 만들어봤습니다.

- 매우 간단합니다.

---

# 참고

> <a href="https://velog.io/@lovesyung/My-First-Todo-Project-tailwind.config.js-%ED%8C%8C%EC%9D%BC-%EC%84%A4%EC%A0%95-%ED%86%B5%ED%95%B4%EC%84%9C-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%83%89%EC%83%81-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0-%EB%8F%84%EC%A0%84">[My-First-Todo-Project] tailwind.config.js 파일 설정 통해서 커스텀 색상 설정하기 도전!</a>
> 
> <a href="https://velog.io/@dmk-jongwho/Tailwind-CSS-%EC%A0%81%EC%9A%A9%EA%B8%B01%ED%8E%B8-%EC%84%A4%EC%B9%98-%EC%82%AC%EC%9A%A9">Tailwind CSS 적용기1편 (설치, 사용)</a>


