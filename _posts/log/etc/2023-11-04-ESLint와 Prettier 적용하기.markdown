---
layout: post
related_posts:
  - /log/etc/2023-11-15-husky, lint-staged 적용하기.markdown
title: Vercel 배포 Application error
categories:
  - log
  - etc
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  Vercel 배포 Application error
---

* toc
{:toc}

> 컨벤션이란?
소프트웨어 개발에서 컨벤션은 코드 작성, 프로젝트 구조, 네이밍 규칙, 코드 스타일 등을 포함하는 일련의 규칙과 가이드 라인을 의미합니다.
이러한 컨벤션은 개발자들이 협업하고 소프트웨어를 더 쉽게 이해하고 유지보수하는 데 도움을 줍니다.


# ESLint

자바스크립트에서 발견되는 문제시되는 패턴을 식별하기 위한 정적 코드 분석 도구 입니다. 
한 마디로 코드 문법 검사와 코딩 스타일 등을 검사해준다고 생각하면 편합니다.
Lint를 통해 잡아내고 싶은 **에러의 기준을 직접 설정**할 수 있습니다.


# ESLint 설정

## (1) 패키지 설치

명령어를 통해 패키지를 설치합니다.

```shell
npm install eslint --save-dev
# or
yarn add eslint --dev
```

## (2) 설정 파일

패키지 설치 후 설정 파일을 세팅을 해줍니다.

```shell
npx eslint --init
   # or
yarn run eslint --init
```

>√ How would you like to use ESLint? · style
√ ESLint를 어디에 사용할거야?
</br>
√ What type of modules does your project use? · esm
√ 프로젝트에서 어떤 타입의 모듈을 사용할거야?
</br>
√ Which framework does your project use? · react
√ 프로젝트에서 어떤 프레임워크를 사용할거야?
</br>
√ Does your project use TypeScript? · No / Yes
√ TypeScript를 사용할거야?
</br>
√ Where does your code run? · browser
√ 코드를 어디에서 실행 시킬거야?
</br>
√ How would you like to define a style for your project? · guide
√ 프로젝트 스타일을 어떻게 정의할거야?
</br>
√ Which style guide do you want to follow? · standard-with-typescript
√ 어떤 스타일 가이드를 할거야?
</br>
√ What format do you want your config file to be in? · JavaScript
√ 파일 포맷 어떤걸로 할거야?
</br>
√ Would you like to install them now? · No / Yes
√ 지금 설치할거야?
</br>
√ Which package manager do you want to use? · yarn
√ 어떤 패키지 관리자를 사용할거야?
</br>

그러면 이런 질문들이 나오는데, 본인 상황에 맞게 선택을 하시면 됩니다.

최종 선택을 하고 나면 .eslintrc.js 이라는 파일이 생깁니다.

<img src="/assets/img/etc/eslint.png" alt="eslint" />

```javascript
module.exports = {
  env: {
    browser: true,
    es2021: true,
  },
  extends: ["standard-with-typescript", "plugin:react/recommended"],
  overrides: [
    {
      env: {
        node: true,
      },
      files: [".eslintrc.{js,cjs}"],
      parserOptions: {
        sourceType: "script",
      },
    },
  ],
  parserOptions: {
    ecmaVersion: "latest",
    sourceType: "module",
  },
  plugins: ["react"],
  rules: {},
};

```
## (3) 설정 정리 

### env
env는 ESLint를 적용할 환경을 설정하는 부분입니다.

```javascript
env: {
  browser: true,
  node: true,
  globals: {
    $: true
  }
}
```
browser, node를 설정하지 않을 경우 **console, require** 같은 사전 정의된 전역변수 환경에 있는 static 메서드를 인식할 수 없어서 에러가 발생합니다.

선언되지 않은 전역변수를 사용하는 경우, **globals**를 이용하여 사용자 전역 변수를 추가할 수 있다.


### extends
extend는 추가한 플로그인에서 사용할 규칙을 설정합니다.

```javascript
extends: [
  "standard-with-typescript",
  "plugin:react/recommended" 
],
```


### parser
parser는 코드를 ESLint에 맞게 파싱하기 위해 사용합니다
값을 주지 않으면 기본적으로 <span style="color:green">"Expree"</span>로 설정됩니다.

> Babel과 함께 사용되는 파서로는 babel-eslint가 있습니다.
 Typescript 구문 분석을 위해 사용되는 @typescript-eslint/parser가 있습니다.
 
 ```javascript
{
    "parser": "babel-eslint",
     # or
    "parser": "@typescript-eslint/parser"
}
```

### parserOptions 
parserOPtions은 ESLint를 사용을 위해 지원하려는 Javascript 언어 옵션을 지정할 수 있습니다.

```javascript
{
  parserOptions: {
    ecmaVersion: 6,
    sourceType: "module",
    ecmaFeatures: {
      "jsx": true
    }
  }
}
```


>- **ecmaVersion**
사용할 ECMAScript 버전을 설정
</br>
- **sourceType**
parser의 export 형태를 설정
</br>
- **ecmaFeatures**
ECMAScript의 언어 확장 기능을 설정
   - **globalReturn**: 전역 스코프의 사용 여부
   - **impliedStric**: strict mode 사용 여부
   - **jsx**: ECMScript 규격의 JSX 사용 여부
   

### plugins
ESLint는 다양한 설정을 위한 외부 플로그인을 지원하는데, 사용하기 전에 패키지 다운로드가 필요합니다.

```javascript
{
  plugins: [
    "react"
  ]
}
```

### rules
ESLint에는 프로젝트에서 사용하는 규칙을 수정할 수 있습니다.
> - **"off" or 0**
규칙을 사용하지 않음
</br>
- **"warn" or 1**
규칙을 경고로 사용
</br>
- **"error" or 2**
규칙을 오류로 사용

규칙은 [공식 문서](https://eslint.org/docs/latest/rules/)에서 확인할 수 있습니다.
플로그인에서 규칙을 지정할 때는 eslint-plugin-을 반드시 생략해야 합니다. 

## ※ 추가 설정
Next.JS에서는 한가지 추가적인 설정이 필요합니다.
React를 사용하면 파일마다 import를 해줘야하는데, Next.JS에서는 그러지 않아도 됩니다. 
그런데 react ESLint는 import를 강제하고 있기 때문에 린트를 적용하면 <span style="color:red">'React' must be in scope when using JSX</span> 라는 에러가 뜹니다.

여기서 rule을 하나 세팅해주면 해결됩니다.

```javascript
rules: {
  "react/react-in-jsx-scope": "off",
},
```

- - -

# Prettier

Prettier는 코드를 포맷팅(정렬)하는 '코드 포맷터' 입니다. 
**Prettier는 코드 스타일**에 초점을 맞추고 유지할 수 있게 도와줍니다.
**코드 품질을 위해 사용하는 ESLint**와는 성격이 다릅니다.

## (1) 패키지 설치

명령어를 통해 패키지를 설치합니다.

```shell
npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier
# or
yarn add -D prettier eslint-config-prettier eslint-plugin-prettier
```

> - prettier
prettier 사용 위한 패키지
</br>
- eslint-config-prettier
ESLint의 포맷팅 관련 설정 중 Prettier와 충동하는 부분을 비활성화 해줍니다.
Prettier에서 문법 관련된 ESLint 규칙을 사용하지 않게 되기 때문에 ESLint는 자바스크립트 문법을 담당하고, 코드 스타일 정리는 Prettier이 담당합니다.
</br>
- eslint-plugin-prettier
원하는 형식의 포맷팅을 설정해줍니다.

## (2) 설정 파일

<img src="/assets/img/etc/폴더 구조.png" alt="eslint" />

프로젝트 루트에 **.prettierrc** 파일을 하나 만들어줍니다.

```shell
{
  "semi": false,
  "trailingComma": "all",
  "singleQuote": false,
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false
}
```
> - semi
코드는 ;(세미콜론)으로 끝나지 않게 합니다.
</br>
- trailingComma
객체나 배열을 작성할 때, 원소 또는 key-value의 맨 뒤에 있는 것에도 쉼표를 붙입니다.
</br>
- singleQuote
문자열을 사용할 때에는 ""사용합니다.
</br>
- printWidth
한 줄이 80칸이 넘지 않도록 합니다.
</br>
- tabWidth
들여쓰기 크기는 2칸입니다.
</br>
- useTabs
tab 대신에 스페이스를 사용합니다.

다른 옵션들은 [prettier 문서](https://prettier.io/docs/en/options.html)에서 확인할 수 있습니다.

# IDE 설정 (VSCode)
자동수정 설정을 위해 IDE 설정을 해줘야합니다.

루트 경로에서 **.vscode 폴더**를 생성하고 **setting.json 파일**을 생성해줍니다.

<img src="/assets/img/etc/폴더 구조1.png" alt="eslint" />

```javascript
{
	"editor.formatOnSave": true,
	"[javascript]": {
		"editor.formatOnSave": false
	},
	"editor.codeActionsOnSave": {
		"source.fixAll.eslint": true
	},
	"eslint.alwaysShowStatus": true,
	"files.autoSave": "onFocusChange"
}
```

해당 설정들을 통해 prettier, eslint가 자동 저장에 반영됩니다.

- - -

# 참고 자료
https://blog.pumpkin-raccoon.com/74
https://lakelouise.tistory.com/199
https://velog.io/@kyusung/eslint-config-2

