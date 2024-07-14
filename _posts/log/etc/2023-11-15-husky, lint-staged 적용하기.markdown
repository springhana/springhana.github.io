---
layout: post
related_posts:
  - /log/etc/2023-11-04-ESLint와 Prettier 적용하기.markdown
  - /log/etc/2024-02-18-좋은 코드 작성하기.markdown
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

# husky\
husky는 Git hook 사용을 도와주는 라이브러리입니다. 
Git에서 특정 이벤트(add, commit, push 등)을 실행할 때, 그 이벤트에 hook을 설정하여 hook에 설정된 스크립트를 실행할 수 있습니다.

## 1) Git hook 이란?

>Git도 다른 버전 관리 시스템처럼 어떤 이벤트가 생겼을 때 자동으로 특정 스크립트를 실행하도록 할 수 있습니다..
>이 hook은 client hook과 server hook으로 나눌수 있습니다.

- client hook
commit, Merge 할때 실행됩니다.
- server hook
push할때  서버에서 실행됩니다.

husky를 사용하면 git을 사용할 때 특정 스크립트를 실행시킬 수 있는 라이브러리입니다.

## 2) 어디에 사용할건데?
보통 협업을 진행할때 prettier나 ESLint로 규칙을 정해서 코딩을 진행하는데, 가끔식 ESLint, Prettier가 적용되지 않고 Git에 올라오는 경우가 존재합니다.
이런 경우를 방지하기 위해 husky를 이용하면, Git에 특정 이벤트(add, commit, push 등)가 일어나기 전에 자동으로 prettier나 ESLint가 작동할 수 있게 할 수 있습니다.

## 3) 설치
```shell
npm i -d husky
# or
yarn add -D husky

npx husky install
```

## 4) 설정 파일
설치를 했다면 자동으로 프로젝트파일에 .husky 폴더가 생성됩니다.

<img src="/assets/img/etc/폴더 구조2.png" alt="폴더 구조" />

이제 package.json안에 scripts 부분에 설정해준다.

```javascript
"scripts": {
	...
	"prepare": "husky install",
	..
}
```

# lint-staged
lint-staged는 stage 상태의 git 파일에 대해 lint와 우리가 설정해둔 명령어를 실행해주는 라이브러리입니다.

>state 상태란 파일들이 git add로 커밋 대상이 된 상태를 stage 상태라고 합니다.

## 1) 어디에 사용할건데?
husky만 사용하면 프로젝트의 모든 코드를 검사하기때문에 비효율적입니다.
그래서 lint-staged를 사용해서 Git의 staged한 코드(git add)만 검사해서, 보다 효율적이게 검사를 해줄 수 있습니다.

## 2) 설치

```shell
npm i -d lint-staged
# or
yarn add -D lint-staged
```

## 3) 설정 파일
package.json안에서 정해준다.
```javascript
"lint-staged": {
    "*.{js,jsx,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
```
프로젝트에서 {js,jsx,ts,tsx} 파일에 대한 검사하는 설정입니다.

## 4) pre-commit 추가

```shell
npx husky add .husky/pre-commit "npm run lin-staged" 
# or
npx husky add .husky/pre-commit "npx lint-staged" 
# or
npx husky add .husky/pre-commit "yarn lint-staged"
```
이제 이러면 .husky안에 pre-commit이라는 파일이 생깁니다.

<img src="/assets/img/etc/폴더 구조3.png" alt="폴더 구조" />

저는 npx husky add .husky/pre-commit "yarn lint-staged"으로 설치했습니다.

```javascript
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

yarn lint-staged
```
설치가 끝나면 pre-commit이라는 파일에 위와 같은 내용이 생깁니다.

이제 적용이 됬는지 확인해보면 됩니다.

# 참고 자료

>https://velog.io/@jma1020/husky-lint-staged%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80
</br>
https://velog.io/@do_dadu/husky-lint-staged%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EC%9E%90-sub-ESLint-%EC%9E%90%EB%8F%99%ED%99%94%ED%95%98%EA%B8%B0
</br>
https://ms3864.tistory.com/412
</br>
https://blog.pumpkin-raccoon.com/85
