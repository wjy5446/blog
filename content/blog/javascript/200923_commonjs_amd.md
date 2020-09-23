---
title: 'AMD와 CommonJS는 무엇이고, 이것들에 대해서 어떻게 생각하시나요?'
date: 2020-9-23 16:21:13
category: 'javascript'
draft: false
---

```
AMD와 CommonKS에 대해서 정리한 글입니다.
```

모듈은 어플리케이션을 비슷한 기능끼리 묶어서 재사용 가능한 코드 조작을 말한다. 보통은 모듈은 파일 단위로 나눠져 있다가 애플리케이션의 로드에 의해서 불러 들어오도록 한다. 기능적으로 코드가 분리 되어 있기 때문에 유지보수성을 높이고, 재사용이 가능하기 때문에 개발의 효율성을 높힐 수 있는 장점을 가지고 있다. 또한 공개된 라이브러리를 로드하여 손 쉽게 개발을 가능하도록 도와준다.

이런 모듈 기능은 대부분의 언어에서 지원하고 있다. 하지만 자바스크립트의 경우에는 모듈 기능을 제공하고 있지 않는다. js 파일로 여러 개로 나눠져 있더라도 script 태그로 가져올 경우 하나의 전역 객체를 공유하도록 설계되어 있다. 그렇기 때문에 변수의 이름이 겹치게 되면 에러가 나는 문제를 가지고 있다.

이런 문제를 해결하기 위해서 IIFE을 사용한 모듈패턴을 사용하기도 했다. 하지만 이런 모듈 패턴은 의존성 관리 매커니즘을 제공하지 않는다.

```javascript
var module = (function() {
  function sayHello() {
    console.log('Hello')
  }

  return {
    sayHello: sayHello,
  }
})()

moduel.sayHello()
```

그래서 자바스크립트 영역에서는 `CommonJS`와 `AMD(Asynchronous Module Definition)`이 제안되었다. 그럼 해당 기술에 대해서 알아본다.

## 1. CommonJS

`CommonJS`는 브라우저 뿐만 아니라 서버사이드, 데스크탑 등에서도 범용적인 언어로 자바스크립트를 사용하기 위해서 나온 그룹이다. 해당 그룹은 Javascript를 범용 언어로 만들기 위해서 Specification을 만드는 역활을 했다. 이런 Spec을 만들기 위해서는 핵심은 모듈화를 하는 것이였다.

여기서는 모듈화를 세 부분으로 이루어진다고 정의했다.

```
- Scope : 모든 모듈은 자신만의 독립적인 실행 영역이 있다.
- Definition : 모듈 정의는 exports 객체를 이용한다.
- Usage : 모듈의 사용은 require 함수를 이용한다.
```

만약 fileA.js의 함수를 fileB.js에서 사용한다고 하면 아래와 같이 사용하면 된다.

```javascript
// fileA.js
var a = 3
var b = 4

exports.sum = function(c, d) {
  return a + b + c + d
}
```

```javascript
// fileB.js
var a = 5
var b = 6

var moduleA = require('fileA')
moduleA.sum(a, b)
```

위 방식은 fileA.js와 fileB.js가 모두 존재하는 서버사이드에서 손쉽게 된다. 하지만 브라우저에서는 필요한 모듈을 모두 전송받을 때까지 사용이 불가능하다. 이를 위해서 script 태그를 동적으로 삽입하는 방법을 사용했다. 하지만 script 태그를 이용한 방식은 변수를 덮어쓰게 되는 문제를 가지고 있다. 그렇게 때문에 CommonJS에서는 `모듈 전송 포맷`을 이용해서 비동기적으로 브라우저에서 로드 되도록 만들었다.

```javascript
// 모듈 전송 포맷
require.define({"cpx-nums/plus-two": function(reqire, exports){
  var sum = require("./cpx-num").sum;

  exports.plus-two = function(a){
    return sum(a, 2);
  };
}, ["cpx-numbers/math"]}); // 먼저 로드할 모듈
```

이런 방식을 사용하고 있는 프로젝트가 Node.js이다.

## 2. AMD

AMD도 자바스크립 표준을 만들기 위해서 나온 그룹이며, Javascript 모듈을 위해서 CommonJS와 같이 논의했지만, CommonJS는 Javascript의 서버사이드에서 사용에 초점를 둔 반면에 AMD는 브라우저에서 실행을 중점을 두었기 때문에 서로 분리되었다.

AMD는 브라우저 환경에서 모듈을 사용할 수 있도록 표준을 만드는 데에 초점을 맞추었다.

브라우저에서는 파일 스코프가 없기 때문에 define 함수를 통해서 파일 스코프를 나누었다. 해당 모듈은 Lazy-Load 기법을 통해서 필요한 시점에 로드되도록 한다.

```javascript

// define(모듈 식별 id, 먼저 로드될 모듈, 팩토리 함수)

define("alpha", ["require", "export", "beta"], fucntion (require, exports, beta) {
  exports.verb = function() {
    return beta.verb() // 넘겨 받은 인수 사용
    return require("beta").verb(); // require을 이용해 모듈 사용
  }
})
```

AMD을 지원하는 프로젝트는 RequireJS가 있다.

## 3. CommonJS vs AMD

서버사이드에서는 CommonJS 명세가 AMD보다 간결하다. 하지만 브라우저 환경에서는 AMD가 CommonJS보다 유연한 방법을 제공한다.

## 참고 자료

- https://poiemaweb.com/es6-module
- https://d2.naver.com/helloworld/12864
- https://skout90.github.io/2017/09/01/Javascript/7.%20javascript-%EB%AA%A8%EB%93%88%ED%99%94/
