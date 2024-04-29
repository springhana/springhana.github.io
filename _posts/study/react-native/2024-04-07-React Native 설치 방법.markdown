---
layout: post
related_posts:
  - /study/typescript/
title: React Native 설치 방법
categories:
  - study
  - react-native
image: /assets/img/react-native/React Native.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  React Native 설치 및 환경 설정
---

* toc
{:toc}

리액트 네이티브를 개발하기 위해서 `Expo`와 `React-Native-Cli`를 사용한다.

보통 `Expo`를 사용하다 익숙해지면 `React-Native-Cli`를 사용하라고 하는데, `Expo`를 사용하면 제공되는 라이브러리가 아니면 사용할 수 없고 용량이 크다고 하여 처음부터 `React-Native-Cli`로 진행하려고 합니다. 

- `React-Native-Cli`
- `window`일 경우 `Android Studio`
- `ios`일 경우 

|                  | 장점                                                                                             | 단점                                                                                       |
| ---------------- | ---------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| React-Native-Cli | 🛠️ Native 모듈 사용 자유도가 높음<br>🛠️ 모듈 직접 제작 가능<br>🛠️ 원하는 언어로 추가 작성 가능<br>🛠️ 빌드 제어 가능            | 🛠️ 프로젝트 환경 구축 및 개발 시각 소유<br>🛠️ 각각의 라이브러리 설치 필요<br>🛠️`Android Studio`, `XCode` 설치하여 작업 |
| Expo             | 🛠️ 앱을 쉽게 만들고 빌드 가능<br>🛠️ `Android Studio`, `XCode` 설치없이 작업<br>🛠️ 다양한 API 지원<br>🛠️ 파일 자동 관리 | 🛠️ 제공되는 API만 사용 가능<br>🛠️ 빌드 시간 10~30분 소요<br>🛠️ 제공해주는 기능과 모듈만 사용 가능                    |

설치 방법 및 순서

1. `Node.js` 설치
2. `React Native CLI` 설치
3. `JDK` 설치
4. 안드로이드 스튜디오 설치
5. 프로젝트 생성 및 실행

---

# <a href="https://nodejs.org/ko/">Node.js</a> 설치

- 다운로드한 msi 파일을 실행시켜 Nodejs 설치를 시작합니다.

<img src="/assets/img/react-native//assets/img/react-native/node 설치.png" />

- 동의 후 다음 버튼을 눌러줍니다.

<img src="/assets/img/react-native/node 설치 1.png" />

- 저장할 경로를 선택하고 다음 버튼을 눌러줍니다.

<img src="/assets/img/react-native/node 설치 2.png" />

- 설정 기본으로 두고 다음 버튼을 눌러 계속 진행합니다.

<img src="/assets/img/react-native/node 설치 3.png" />

<img src="/assets/img/react-native/node 설치 4.png" />

- install 버튼을 눌러 설치를 완료합니다.

<img src="/assets/img/react-native/node 설치 5.png" />

<img src="/assets/img/react-native/node 설치 6.png" />

## 설치 버전 확인하기

```shell
node -v  
npm -v
```

<img src="/assets/img/react-native/node 확인.png" />

<img src="/assets/img/react-native/npm 확인.png" />

---

#  <a href="https://reactnative.dev/">React-Native-Cli</a> 설치

- npm 명령어로 React-Native-Cli를 설치해 줍니다. 자동으로 패키지가 설치되는 것을 확인할 수 있습니다.

```shell
npm install -g react-native-cli
npm install -g react-native
```

<img src="/assets/img/react-native/react native 설치.png" />

## 설치 버전 확인하기

- 리액트 네이티브 버전을 확인하면 정상적으로 설치되었는지 확인할 수 있습니다.

```shell
react-native -v
```

<img src="/assets/img/react-native/react native 확인.png" />

---
# <a href="https://www.oracle.com/kr/java/technologies/downloads/">JDK</a> 설치

본인의 환경에 맞는 운영체제의 JDK를 설치해 줍니다. 설치 시 오라클 계정이 필요합니다.

## 환경변수 설정

- JDK 설치 후 환경변수를 설정 해야합니다.
	- 내 PC > 우클릭 속성 > 고급 시스템 설정으로 들어와서 환경변수 버튼을 선택합니다.

<img src="/assets/img/react-native/Java 환경 설정 1.png" />

- 사용자 변수를 새로 만들어서 JAVA_HOME을 설정해 줍니다.
	- _변수 이름 : JAVA_HOME_
	- _변수 값 : JDK 설치 위치_

<img src="/assets/img/react-native/Java 환경 설정 2.png" />

- 사용자 변수를 등록한 후, 하단의 시스템 변수 Path도 수정해줘야 합니다.
	- Path 편집 > 새로 만들기를 눌러 %JAVA_HOME%\bin을 작성하고 확인 버튼을 클릭합니다.

<img src="/assets/img/react-native/Java 환경 설정 3.png" />

## 설치 버전 확인하기

```shell
java -version
```

<img src="/assets/img/react-native/Java 확인.png" />

---

# <a href="https://developer.android.com/studio?hl=ko">Android Studio</a> 설치

- 다운로드한 파일을 실행시키고 Next 버튼을 클릭합니다.

<img src="/assets/img/react-native/안드로이드 설치.png" />

- Android Virtual Device에 선택이 되어있는지 확인한 후 Next를 클릭합니다.

<img src="/assets/img/react-native/안드로이드 설치 1.png" />

- 설치될 경로를 설정한 후 Install 버튼을 클릭합니다.

<img src="/assets/img/react-native/안드로이드 설치 2.png" />

<img src="/assets/img/react-native/안드로이드 설치 3.png" />

<img src="/assets/img/react-native/안드로이드 설치 4.png" />

- 설치가 완료되었습니다.

<img src="/assets/img/react-native/안드로이드 설치 5.png" />

- 기본 설정을 세팅하기 위해 안드로이드 스튜디오를 실행합니다.

<img src="/assets/img/react-native/안드로이드 설치 6.png" />

- 구글에서 데이터를 수집한다는 내용과 SDK 관련하여 공지하고 있습니다.

<img src="/assets/img/react-native/안드로이드 설치 7.png" />

<img src="/assets/img/react-native/안드로이드 설치 8.png" />

- 설치할 타입을 선택 후 다음으로 갑니다.

<img src="/assets/img/react-native/안드로이드 설치 9.png" />

- 설치한 JDK의 위치를 설정한 후 다음을 눌러줍니다.

<img src="/assets/img/react-native/안드로이드 설치 10.png" />

- 사용할 테마를 선택 후 다음을 눌러줍니다.

<img src="/assets/img/react-native/안드로이드 설치 11.png" />

- Android Virtual Device를 선택하면 안드로이드 스튜디오가 설치되면서 가상 단말장치가 자동으로 설정됩니다.

<img src="/assets/img/react-native/안드로이드 설치 12.png" />

<img src="/assets/img/react-native/안드로이드 설치 13.png" />

<img src="/assets/img/react-native/안드로이드 설치 14.png" />

- 안드로이드 스튜디오 설정이 완료되었습니다.

<img src="/assets/img/react-native/안드로이드 설치 15.png" />

## 환경변수 설정

- 환경변수 설정을 위해 내 PC > 속성 >고급 시스템 설정으로 들어갑니다.

<img src="/assets/img/react-native/안드로이드 설치 16.png" />

<img src="/assets/img/react-native/안드로이드 설치 17.png" />

- 시스템 속성 > 고급 > 환경변수를 눌러줍니다.

<img src="/assets/img/react-native/안드로이드 설치 18.png" />

- 시스템변수에서 ANDROID_HOME을 설정해 줍니다.
	- _변수 이름 : ANDROID_HOME_
	- _변수 값 : SDK 설치 위치_

<img src="/assets/img/react-native/안드로이드 설치 19.png" />

```shell
C:\Users\사용자\AppData\Local\Android\Sdk\tools

C:\Users\사용자\AppData\Local\Android\Sdk\platform-tools

C:\Users\사용자\AppData\Local\Android\Sdk\emulator

C:\Users\사용자\AppData\Local\Android\Sdk\build-tools
```

- Android SDK 설치 위치는 Android Studio Settings에서 확인할 수 있습니다.
	- 경로 : File > Settings > Appearance & Behavior > System Settings > Android SDK

<img src="/assets/img/react-native/안드로이드 설치 20.png" />

- 시스템변수에 추가했다면 사용자변수에도 추가해줘야 합니다.
	- 사용자 변수 > Path 수정에 들어가 %ANDROID_HOME%\platform-tools을 새로 만들어 줍니다.

<img src="/assets/img/react-native/안드로이드 설치 21.png" />

## 설치 버전 확인하기

```shell
adb --version
```

<img src="/assets/img/react-native/안드로이드 설치 22.png" />

---

# 프로젝트 실행

##  **React Native 신규 프로젝트 생성**

- react-native-cil이 문제일 경우
```shell
npm uninstall -g react-native-cli @react-native-community/cli
```

```shell
npx react-native@latest init 프로젝트명
```

## ***안드로이드 실행**

```shell
npm run android  
또는  
react-native run-android
```

## 오류

```shell
TypeError: cli.init is not a function
```

```shell
Installing react-native...
Consider installing yarn to make this faster: https://yarnpkg.com
npm WARN deprecated source-map-url@0.4.1: See https://github.com/lydell/source-map-url#deprecated
npm WARN deprecated urix@0.1.0: Please see https://github.com/lydell/urix#deprecated
npm WARN deprecated source-map-resolve@0.5.3: See https://github.com/lydell/source-map-resolve#deprecated
npm WARN deprecated resolve-url@0.2.1: https://github.com/lydell/resolve-url#deprecated
npm WARN deprecated uglify-es@3.3.9: support for ECMAScript is superseded by `uglify-js` as of v3.13.0

added 652 packages, and audited 653 packages in 7s

33 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
C:\Users\YUN\AppData\Roaming\npm\node_modules\react-native-cli\index.js:302
  cli.init(root, projectName);
      ^

TypeError: cli.init is not a function
    at run (C:\Users\YUN\AppData\Roaming\npm\node_modules\react-native-cli\index.js:302:7)
    at createProject (C:\Users\YUN\AppData\Roaming\npm\node_modules\react-native-cli\index.js:249:3)
    at init (C:\Users\YUN\AppData\Roaming\npm\node_modules\react-native-cli\index.js:200:5)
    at Object.<anonymous> (C:\Users\YUN\AppData\Roaming\npm\node_modules\react-native-cli\index.js:153:7)
    at Module._compile (node:internal/modules/cjs/loader:1159:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1213:10)
    at Module.load (node:internal/modules/cjs/loader:1037:32)
    at Module._load (node:internal/modules/cjs/loader:878:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:81:12)
    at node:internal/main/run_main_module:23:47
```

해당 오류는 버전이 맞지않아 발생한 오류입니다.

react-native 버전을 낮춰 프로젝트를 실행하면 정상적으로 생성됩니다.

```shell
npx react-native init 프로젝트명 --version 0.68.2
```