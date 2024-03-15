---
layout: post
related_posts:
  - _posts/study/javascript/2024-03-07-프로토타입 (2).markdown
title: strict mode
categories:
  - study
  - javascript
image: /assets/img/javascript/modern-javascript-book.png
permalink: '/:categories/:year/:month/:day/:title/'
description: >
  모던 자바스크립트 Deep Dive: 자바스크립트의 기본 개념과 동작 원리 - 20장 strict mode
---

* toc
{:toc}

---

# 1. strict mode란?

```js
function foo() {
  x = 10;
}
foo();

console.log(x); // ?
```

위의 예제에서 전역 객체의 `x` 프로퍼티는 마치 전역 변수처럼 사용할 수 있다. 이러한 현상을 **암묵적 전역(implicit global)** 이라 한다.

- 개발자가 의도와는 상관없이 발생하여 오류의 원인이 될 가능성이 크다. 
- 반드시 `var`, `let`, `const` 키워드를 사용해야 한다.

잠재적인 오류를 발생시키기 어려운 개발 환경을 만들고 그 환경에서 개발하기 위해 지원하는 ES5부터 strict mode(엄격 모드)가 추가되었다.

> strict mode는 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킨다.

- ESLint 같은 린트 도구를 사용해도 strict mode와 유사한 효과를 얻을 수 있다. 

린트 도구는 정적 분석기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류만이 아니라 잠재적 오류까지 찾아내고 오류의 원인을 리포팅해주는 도구다.

- 클래스와 모듈은 기본적으로 strict mode가 적용된다.

---
# 2. strict mode의 적용

전역의 선두 또는 함수 몸체의 선두에 `'use strict';`를 추가한다.

- 전역의 선두에 추가하면 스크립트 전체에 적용
```js
'use strict';

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

 - 함수 몸체의 선두에 추가하면 해당 함수와 중첩 함수에 적용
 ```js
 function foo() {
  'use strict';

  x = 10; // ReferenceError: x is not defined
}
foo();
```

- 코드의 선두에 위치 안시키면 작동 안함
```js
function foo() {
  x = 10; // 에러를 발생시키지 않는다.
  'use strict';
}
foo();
```

---
# 3. 전역에 strict mode를 적용하는 것은 피하자

전역에 적용한 `strict mode`는 스크립트 단위로 적용된다.
```js
<!DOCTYPE html>
<html>
<body>
  <script>
    'use strict';
  </script>
  <script>
    x = 1; // 에러가 발생하지 않는다.
    console.log(x); // 1
  </script>
  <script>
    'use strict';

    y = 1; // ReferenceError: y is not defined
    console.log(y);
  </script>
</body>
</html>
```

위 예제의 같이 스트립트 단위로 적용한 strict mode는 다른 스크립트에 영향을 주지 않고 해당 스크립트에 한정되어 적용된다.

`strict mode` 스크립트와 `non-strict mode` 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다.

- 외부 서드파티 라이브러리 경우 `non-strict mode`인 경우도 있기 때문에 전역에 `strict mode`를 적용하는 것은 바람직하지 않다.

이럴때 즉시 실행 함수로 스크립트 전체를 감싸서 스코프를 구분하고 즉시 실행 함수의 선두에 `strict mode`를 적용한다.
```js
// 즉시 실행 함수의 선두에 strict mode 적용
(function () {
  'use strict';

  // Do something...
}());
```

---
# 4. 함수 단위로 strict mode를 적용하는 것도 피하자

함수 단위로 `strict mode`를 적용할 수도 있다.

- `strict mode`가 적용된 함수가 참조할 함수 외부의 컨텍스트에 `strict mode`를 적용하지 않는다면 이 또한 문제가 발생할 수 있다.

```js
(function () {
  // non-strict mode
  var lеt = 10; // 에러가 발생하지 않는다.

  function foo() {
    'use strict';

    let = 20; // SyntaxError: Unexpected strict mode reserved word
  }
  foo();
}());
```

> `strict mode`는 즉시 실행 함수로 깜싼 스크립트 단위로 적용하는 것이 바람직하다.

---
# 5. strict mode가 발생시키는 에러

## 암묵적 전역

선언하지 않은 변수를 참조하면 에러 발생

```js
(function () {
  'use strict';

  x = 1;
  console.log(x); // ReferenceError: x is not defined
}());
```

## 변수, 함수, 매개변수의 삭제

`delete` 연산자로 변수, 함수, 매개변수를 삭제하면 에러 발생

```js
(function () {
  'use strict';

  var x = 1;
  delete x;
  // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo;
  // SyntaxError: Delete of an unqualified identifier in strict mode.
}());
```

## 매개변수 이름의 중복

중복된 매개변수 이름을 사용하면 에러가 발생

```js
(function () {
  'use strict';

  //SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
}());
```

## with 문의 사용

`with` 문을 사용하면 에러 발생

```js
(function () {
  'use strict';

  // SyntaxError: Strict mode code may not include a with statement
  with({ x: 1 }) {
    console.log(x);
  }
}());
```

---
# 6. strict mode 적용에 의한 변화

## 일반함수의 this

`strict mode`에서 함수를 일반 함수로서 호출하면 `this`에 `undefined`가 바인딩된다.

```js
(function () {
  'use strict';

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
}());
```

## arguments 객체

`strict mode`에서 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

```js
(function (a) {
  'use strict';
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0: 1, length: 1 }
}(1));
```