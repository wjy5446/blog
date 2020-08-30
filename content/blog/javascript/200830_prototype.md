---
title: 'Prototype : JS의 상속'
date: 2020-8-30 16:21:13
category: 'javascript'
draft: false
---

```
다양한 블로그를 읽고 저 나름으로 Execution Context을 정리한 글입니다.
```

프로그래밍에서 최대한 중복된 코드를 줄이는 것은 매우 중요한 작업이다. 이를 위해서 나온 개념이 바로 `상속`이다. 상속을 통해서 다양한 객체의 공통된 프로퍼티와 메소드를 상속하여, 효율적인 코드 작성을 가능하도록 도와준다. C나 Python인 경우에는 보통은 `Class`라는 개념을 통해서 상속이 이루어진다. 하지만 Javascript는 Class가 아니라 `Prototype`을 기반으로 상속이 이루어진다. 기존의 C++이나 Python에 익숙한 나로써는 `Prototype`에 대한 개념은 매우 낯설기만 하다. 해당 개념을 이해하려고 하니 Class가 떠올라서 매우 헷갈렸다. 또한 해당 개념에 대한 글을 읽어보니 prototype, \_\_proto\_\_, [[Prototype]] 등등 비슷한 글자가 많이 나오니 매우 헷갈린다. 그래서 이번에는 Prototype에 대한 내용을 정리해 보려고 한다. 여러 글을 읽고 내 나름대로 정리한 것이니 때문에 틀린 내용이 존재할 수도 있다.

## prototype 객체, 생성자 함수, 객체의 관계

Prototype에 대한 내용을 이해하기 위해서는 우선 `prototype 객체`, `생성자 함수`, `객체`에 대해서 알고, 이들의 관계가 어떻게 연결되는 지 알아야 한다. 해당 요소의 관계를 유산으로 비유하자면 `prototype 객체`는 부모님의 통장이고 `객체`는 자식의 통장이다. 그리고 `생성자 함수`는 유산을 전달하는 부모님이라고 생각하면 편하다. 생성자 함수는 prototype 객체라는 유산이 있는 통장을 가지고 있다. 그래서 부모인 생성자 함수는 자신의 유산을 여러 자식의 통장으로 이동 시켜주어야 한다. 하지만 여러 자식들에게 실제로 돈을 나눠주기가 힘들기 때문에 자신의 유산이 존재하는 통장의 위치만 자식들에게 알려주는 것이다. 그래서 부모인 생성자 함수는 객체인 여러 자식들의 통장을 만들고, 부모의 통장이 위치한 위치을 자신의 통장에 알려준다고 생가하면 된다. 뭔가 예시 때문에 뜬금이 없을 수도 있다. 나는 이렇게 이해하는 것이 편했는 데 이 글을 읽는 사람은 어렵게 받아드릴 수 있다. 그래서 실제로 어떻게 연결되는 지에 대해서 작서애 보려고 한다.

## prototype 프로퍼티, constructor 프로퍼티와 **proto** 프로퍼티

먼저 prototype 객체와 생성자 함수의 관계를 연결해주는 `prototype 프로퍼티`와 `constructor 프로퍼티`에 대해서 알아본다.

`prototype 프로퍼티`는 오직 함수에서만 가지고 있는 프로퍼티로써 함수가 생성자 함수로 사용될 때, prototype 객체가 prototype 프로퍼티에 연결된다. 여기서 prototype 객체에 대해서 설명하면, 상속할 프로퍼티를 가지고 있는 객체이다. 생성자 함수에 의해서 생성되는 객체는 `{생성자 함수 명}.prototype`으로 접근이 가능하다.

`constructor 프로퍼티`는 prototype 객체가 가지고 있는 프로퍼티로써 자신과 연관된 생성자 함수에 연결되어 있다. `{생성자 함수 명}.prototype.constructor`는 자기 자신을 가르킨다.

```javascript
function Person(name) {
  this.name = name
}

var foo = new Person('Lee')

console.log(Person.prototype.constructor === Person)
console.log(foo.constructor === Person)
```

또한 \_\_proto\_\_에 대해서 설명해보려고 한다. \_\_proto\_\_프로퍼티는 모든 객체가 가지고 있는 프로퍼티이다. 함수 또한 객체이기 때문에, 함수도 해당 프로퍼티를 가지고 있다. 원래는 [[Prototype]]이라는 인터널 슬롯을 통해서 부모 prototype 객체를 가르키고 있지만 이를 접근하기 위해서는 \_\_proto\_\_을 통해서 접근할 수 있도록 도와준다.

```javascript
function Person(name) {
  this.name = name
}

var foo = new Person('Lee')

console.log(Person.prototype === foo.__proto__)
```

## Prototype chain

javascript에서는 상속한 프로퍼티에 접근하기 위해서는 Prototype chain이란 기법을 통해서 프로퍼티를 찾아간다. 우선의 현재 자신의 객체안에서 프로퍼티를 찾고, 프로퍼티가 존재하지 않을 경우에는 [[Prototype]] 슬롯에 연결된 부모 객체에 있는 프로퍼티를 확인한다. 만약 부모 객체에 해당 프로퍼티가 없다면 Error가 발생한다.

## 결론

- prototype 객체는 부모 객체이며, [[Prototype]] 슬롯에 의해 자식 객체와 연결있으며, \_\_proto\_\_ 프로퍼티를 통해서 접근이 가능하다.
- constructor 프로퍼티는 prototype 객체 및 객체를 생성한 생성자 함수를 가르키고, prototype 프로퍼티는 생성자 함수만 가지고 있으면 상속할 prototype를 가르킨다.

- 부모 프로퍼티는 [[Prototype]]에 의해서 차근 차근 접근하며, 이런 방식이 Prototype chain이라고 부른다.

## 참고

[https://hackernoon.com/understanding-javascript-prototype-and-inheritance-d55a9a23bde2](https://hackernoon.com/understanding-javascript-prototype-and-inheritance-d55a9a23bde2)
[https://poiemaweb.com/js-function#61-arguments-프로퍼티](https://poiemaweb.com/js-function#61-arguments-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)
[https://codeburst.io/javascript-prototype-cb29d82b8809](https://codeburst.io/javascript-prototype-cb29d82b8809)
[https://medium.com/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9](https://medium.com/javascript-scene/master-the-javascript-interview-what-s-the-difference-between-class-prototypal-inheritance-e4cd0a7562e9)
