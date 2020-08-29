---
title: 'Execution Context'
date: 2020-8-29 16:21:13
category: 'javascript'
draft: false
---

```
다양한 블로그를 읽고 저 나름으로 Execution Context을 정리한 글입니다.
```

Javascript에 대해서 깊이 알지 못한다는 생각이 들어서 개념을 알아 보려고 했다. Github에서 `모든 자바스크립트 개발자가 알아야 할 33 개념`이란 repo를 보게 되었다. 해당 목록을 중심으로 블로그를 작성해 보려고 했다. 그래서 처음으로 글을 작성할 개념을 선택하기 위해서 천천히 각 개념을 훑어보았다. 그런데 어떤 글을 읽어도 자주 등장하는 개념이 있었다. 그건 바로 `Execution Context` 였다. 얼마나 중요하길래 항상 등장하는 걸까? 라는 생각이 들었다. 그래서 첫 글로 위 궁금증을 해소하기 위해서 Execution Context 에 대한 글을 작성해 보려고 한다.

## What is Execution Context ?

`Execution Context`는 코드가 실행될 때의 필요한 환경이다. 코드가 실행되기 위해서는 변수(전역변수, 지역변수, 매개변수, 객체 프로퍼티), 함수선언, scope, this와 같은 정보들이 필요한다. 이런 환경을 Execution Context가 제공한다. 실제 Execution Context는 아래와 같이 객체로 존재하면, `Call stack`이란 곳에 저장된다.

Call stack은 이름과 같이 stack 구조를 가지는 Execution Context를 저장하는 공간으로 자바스크립트 엔진이 script 태그를 만나게 되면 Call stack을 생성하게 된다. 함수가 실행되면 해당 함수의 Excution Context를 생성하고 Call stack에 push 한다. 그리고 엔진은 Call stack의 top에 있는 함수가 실행한다. 함수가 종료되면 해당 Execution Context는 Call stack에서 pop 되어 사라지고, 제어를 다음 Top에 있는 함수로 이동하는 방식으로 동작한다.

Execution Context의 종류 크게 3 가지가 존재한다. `Global Execution Context`, `Functional Execution Context`, `Eval Execution Context` 이렇게 존재한다. Eval Execution Context 경우, 많이 사용되지 않는 개념이기 때문에 여기서는 앞에 2 요소만 고려한다. `Global Execution Context`는 Scipt가 실행될 때 생성되는 Execution Context이다. scope는 global scope를 가르키고, this는 window object를 가르킨다. `Functional Execution Context`는 함수가 호출될 때마다 생성된 Execution Context이다.

## Execution Context 구성

Execution Context는 원래는 추상적인 개념이지만 물리적으로 객체형태로 주어진다. 이 객체는 3가지 프로퍼티를 가지고 있다. 구성은 아래와 같고 이를 하나하나 설명해 보려고 한다.

1. Variable object
2. Scope chain
3. thisValue

`Variable object`는 코드가 실행될 때 필요한 정보들을 가지고 있는 객체이다. 여기에 담기는 정보는 Global Execution Context와 Functional Execution Context가 다르다. Global Execution Context는 전역 변수와 전역 함수을 가지고 있고, Functional Execution Context는 paremeter, argument, 내부 함수와 지역 변수를 가지고 있는다.

`Scope chain`은 참조 가능한 scope 리스트 정보를 가지고 있는 개체이다. 자바스크립트에서는 식별자에 해당되는 값을 찾을 때, 현재 scope에서 찾고 없으면 인접한 상위 scope에서 찾아가는 매커니즘을 사용한다. 이를 scope chain 이라고 부른다. 이 정보를 Execution Context에서 Scope chain 객체에 저장한다. Scope chain은 Variable object를 참조하는 리스트로 저장된다. 리스트 상위에는 현재 Variable object를 참조하고, 이후는 인접한 상위 scope를 참조한다. 이런 scope chain은 함수의 프로퍼티인 `[[Scope]]`로 참조가 가능하다.

`this value`은 this에 연결된 객체 정보를 담고 있다. 연결되는 방법은 함수가 어떻게 호출되는 지에 따라서 달라진다. 이는 추후에 정리해서 올려보기로 한다.

## Execution Context 생성 과정

Execution Context는 `Create phase`와 `Execute phase` 이렇게 2단계로 생성된다.

**Create phase**

Create phase에서는 3단계를 통해서 Execution Context가 생성된다. 그 순서는 아래와 같다.

1. Scope Chain 생성과 초기화
2. Variable Instantiation 실행
3. this value 결정

초기에 현재 환경에 연관된 scope chain을 생성하고 초기화한다.

그 다음은 Variable Instantiation 작업이 이루어진다. 이는 변수, parameter, argument, function을 Variable Object에 추가하는 작업을 말한다. Variable Instantiation 내에서도 순서가 있다.

1. (Functional Execution Context)의 경우, {parameter: argument} 형태로 설정
2. 함수 선언을 {함수명: 함수 객체} 형태로 설정
   - 이 때 함수객체는 [[Scope]] 프로퍼티를 가지는 데, 이는 Execution Context의 scope chain을 참조한다. 이때 함수는 [[Scope]]에 scope chain을 가지고 있기 때문에, Execution Context가 사라져도 scope chain 참조할 수 있다. 이를 `Closure`라고 부른다.
   - 여기서 함수가 실행되기 이전에 Variable Instantiation을 통해서 Variable Object는 함수를 가지고 있다. 그래서 함수 선언 이전에 함수를 호출할 수 있다. 이를 `Function Hoisting`이라 부른다.
3. 변수 선언을 {변수명: undefined} 형태로 설정
   - 보통 변수는 Variable Object에 등록하는 선언 단계, undefined로 초기화하는 단계, 실제 값을 할당하는 단계 순서 이루어진다. Create Phase에서는 할당 단계전에 선언 단계와 초기화 단계를 동시화 이루어진다. 그렇기 때문에 할당을 하지 않아도 변수 접근이 가능하다. 이를 `Variable Hoisting`이라고 한다.

마지막으로 this value가 결정된다. 함수 호출되는 패턴에 의해서 this의 값이 결정된다.

**Execution phase**

Execution phase에서는 엔진이 코드를 한줄씩 읽으면서 변수를 만나면 변수를 할당하고 함수 실행을 만나면 함수가 실행 과정을 거친다.

`변수 할당`할 때에는 우선 scope chain에 있는 목록을 보고 값을 할당한다. scope chain에 상단 부터 확인하여 해당 변수명이 존재하면 해당 값을 할당한다.

`함수 실행`할 때에는 우선 `Functional Execution Context`를 생성해 Call stack에 채운다. Execution Context가 생성되면 제어가 생성된 다시 Create phase와 Execution phase가 실행된다.

## 정리

- Execution Context는 코드 실행에 필요한 환경이다.
- Execution Context는 Variable Object, Scope Chain, this를 프로퍼티로 가진 객체이다.
- Execution Context는 Call Stack이란 곳에 쌓이며, Call Stack에 생성이 완료된 상위 Exectuion Context 기준으로 코드를 수행한다.

- Execution Context는 Create phase와 Execution phase로 이루어진다.
- Create phase는 scope chain, Variable Object (parameter, argument -> function -> variable(선언 및 초기화)), this 순으로 이루어진다.
- Execution phase는 한줄 씩 실행되며, 변수를 만나면 값을 할당하고, 함수 실행을 만나면 함수 Execution Context가 생성되 Call Stack에 Push 된다.

## 참고 자료

https://poiemaweb.com/js-execution-context
https://velog.io/@imacoolgirlyo/JS-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%9D%98-Hoisting-The-Execution-Context-%ED%98%B8%EC%9D%B4%EC%8A%A4%ED%8C%85-%EC%8B%A4%ED%96%89-%EC%BB%A8%ED%85%8D%EC%8A%A4%ED%8A%B8-6bjsmmlmgy
https://medium.com/sjk5766/call-stack%EA%B3%BC-execution-context-%EB%A5%BC-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-3c877072db79
