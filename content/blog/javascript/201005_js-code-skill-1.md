---
title: 'this는 Javacript에서 어떻게 작동하는 가?'
date: 2020-9-23 16:21:13
category: 'javascript'
draft: false
---

```
this 동작에 대해서 정리한 글입니다.
```

자바스크립트에서 this은 기존의 java, c와 같이 언어와 다르게 동작한다. 기존 언어에서는 this는 자신 객체를 가르키는 참조 변수이지만, 자바스크립트에서는 함수 호출 방법에 따라서 this의 값이 달라진다.

자바스크립트 함수 호출은 아래와 같이 4가지 방법이 존재한다.

```
함수 호출 방식

1. 일반 함수 호출
2. 메소드 호출
3. 생성자 함수 호출
4. apply, call, bind 호출
5. arrow function 호출
```

## 1.1 일반 함수 호출

기본적으로 this는 global object를 바인딩하고 있는다. 브라우저의 경우에는 window, node의 경우에는 global을 가르킨다.

이 때 일반 함수를 호출하게 되면, this는 global object를 가르킨다.

여기서 주의할 점은 callback 함수나 객체의 메소드 내부 함수에서도 this는 global object를 가르킨다 ;;;

이런 이상한 this 바인딩 방식을 회피하기 위해서는 여러 방법이 존재한다.

첫번째 방법은 메소드 내에 this를 저장한 변수를 생성하고, 해당 변수를 메소드내 내부 함수에 전달하는 방식을 사용한다.

```javascript
var obj = {
  value: 100,
  foo: function() {
    var that = this

    function bar() {
      console.log(that.value) // 100
    }
  },
}
```

두번째 방법은 apply, call, bind를 이용해서 직접 this를 바인딩하는 방법이 존재한다. 해당 방법은 추후 설명하니 아래의 내용을 참조하면 된다.

## 1.2 메소드 호출

객체에서 메소드 함수를 호출하면, this는 메소드 함수를 소유한 객체를 가르키게 된다.

## 1.3 생성자 함수 호출

자바스크립트에서는 일반함수를 new를 이용해서 호출하게 되면 일반함수를 생성자 함수로 사용할 수 있다. 이렇게 함수를 new를 이용해 생성자 함수를 호출하면 아래와 같은 작업이 이루어진다.

```
1. 빈 객체를 생성하고, this는 빈 객체를 가르킴
2. 빈 객체의 생성자 함수의 prototype을 자신의 프로토타입으로 지정
3. this를 통해서 빈 객체의 프로퍼티와 메소드를 입력
4. return이 없는 경우, 생성된 객체를 반환한다. 하지만 return이 있는 경우에는 return 값을 반환. 즉 생성자 함수의 역할을 수행하지 못함
```

이런 과정을 통해서 생성자 함수는 객체를 생성하게 된다. 즉 this는 생성된 객체를 가르키게 된다.

여기에서 주의할 점은 만약 new를 사용하지 않고 생성자 함수를 호출하면 일반 함수 호출 처럼 동작하기 때문에 this가 window에 바인딩 된다. 이 때 아래처럼 global object의 프로퍼티를 덮어쓰는 경우가 발생할 수 있다.

```javascript
function Person(name) {
  this.name = name
}

var me = Person('Lee')

console.log(window.name) // Lee
```

이를 회피하는 방법으로 아래와 같이 Scope-safe Constructor 사용해서 예방할 수 있다.

```javascript
function A(arg) {
  if (!(this instanceof arguments.callee)) {
    return new arguments.callee(arg)
  }

  // 프로퍼티 생성과 값의 할당
  this.value = arg ? arg : 0
}

var a = new A(100)
var b = A(10)

console.log(a.value)
console.log(b.value)
```

## 1.4 apply, call, bind 함수

funtion 객체의 메소드인 apply, call, bind 함수는 명시적으로 this를 지정할 수 있다.

- Function.prototype.apply([바인딩할 객체], [매개변수 배열]) : function 객체의 메소드이며, this 바인딩과 해당 함수를 호출한다.
  사용 예 : 유사 배열 객체에서 배열 객체의 메소드를 이용할 때 사용. 콜백 함수 사용시에도 사용

- Function.prototype.call([바인딩할 객체], 매개변수1, 매개변수2) : apply와 같은 용도. 하지만 apply에서 배열로 입력한 것과 달리, 하나의 인자로 받음

- Function.prototype.bind([바인딩할 객체]) : ES5에 추가된 문법. 함수에 전달된 this로 바인딩된 함수를 리턴, 단, 함수를 호출하지 않아 따로 명시적으로 함수 호출이 필요.

## 1.5 Arrow function

Arrow function은 es6에 추가된 문법으로 함수를 좀더 간단한 방식으로 표현할 수 있게 도와주는 문법이다. 이 문법은 기존 함수와 다른게 표현 방식 말고 또 하나 존재한다. 그건 바로 this를 바인딩하는 방법이 다르다.

기존 함수는 this를 호출 방식에 따라서 결정했다. 하지만 Arrow function은 언제나 상위 스코프의 this를 가르킨다. 이는 `lexical this`라고 부른다.

그래서 arrow function을 사용했을 때, 메소드 내부의 내부함수를 사용했을 때, this는 global object를 가르키는 게 아니라 상위 스코프를 가르키기 때문에 해당 객체를 가르키게 된다. 또한 객체의 메소드를 콜백함수로 사용할 때, 객체를 가르키게 된다. 그래서 위에서 설명한 방법을 사용하지 않아도 된다.

하지만 arrow function을 사용하지 말아야 되는 경우가 있다. 아래의 경우에는 arrow function을 사용해서는 않 된다.

```
1. 객체의 메소드
2. prototype 객체의 메소드
3. 생성자 함수
4. addEventListener 함수의 콜백 함수
```

첫 번째와 두 번째는 상위 스코프가 global object이기 때문에 this가 global object를 가르킨다. 세 번째에는 생성자 함수는 prototype 프로퍼티가 가르키는 객체의 constructor를 사용해서 생성한다. 하지만 arrow function은 prototype이 없기 때문에 생성자 함수로 사용할 수 없다. 네 번째에는 addEventListner에서 콜백 함수로 function를 사용하면 this가 해당 노드를 가르킨다. 하지만 arrow function을 사용하면 this는 window를 가르킨다.

## 참고 자료

- https://poiemaweb.com/js-this
- https://poiemaweb.com/es6-arrow-function
- https://wormwlrm.github.io/2019/03/04/You-should-know-JavaScript-this.html
- https://github.com/FEDevelopers/tech.description/wiki/%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%EB%90%98%EB%8A%94-this%EC%97%90-%EB%8C%80%ED%95%9C-%EC%84%A4%EB%AA%85-1
- https://ko.javascript.info/class
