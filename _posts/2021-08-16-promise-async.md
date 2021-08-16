---
layout: post
title: "Promise, Async/Await"
author: Minsun
categories: [Development]
tags: [javascript]
image: "assets/images/javascript.png"
beforetoc: ""
toc: true
---

## `Promises, Async/Await`

> 자바스크립트에서 코드 작성시에 꽤 많은 비동기적 코드를 다뤄야 한다. 예를 들어 버튼을 클릭하게 되면 스피너가 켜지고 api 요청을 보내게 되고 응답을 받으면 수량이 업데이트되고 스피너가 꺼지고 행위는 완료된다. 이렇게 앞의 태스크의 응답에 따라 다음 태스크가 의존적으로 실행이 되는 것을 비동기적으로 코드를 작성해 줘야 한다. 아래 내용은 이와 관련된 자바스크립트 문법을 정리한 내용이다.

자바스크립트에 Promises 문법이 도입되기 이전에는 콜백함수를 사용하여 네스팅하는 식으로 작성되었다. 이는 콜백 헬이라는 가독성이 좋지 않는 코드 작성 형태가 되었고 이 방법 대신 새로 도입된 문법을 사용하여 비동기 코드를 작성하고 있다.

### `Promise syntax`

promise는 콜백 헬을 해결하기 위해 ES6에서 도입된 문법이다.

```jsx
1. 생성하기
promise 는 Promise 생성자 함수를 사용하여 생성이 가능하다.
해당 생성자 함수는 콜백함수를 인자로 받는다.

콘솔에서 빈 콜백함수를 전달하여 Promise 객체를 생성하면
state를 갖는 객체임을 알 수 있다.
console.log(new Promise(() => {}));
//Promise { <pending> }
//위의 객체를 펼쳐보면 아래와 같이 나온다.
[[Prototype]]: Promise
[[PromiseState]]: 'pending'
[[PromiseResult]]: undefined

2. [[PromiseState]]
Promise의 상태를 나타내는 속성으로 3가지의 값을 가진다.
(1) 'fulfilled': promise가 해결되었을 때(resolved),
어떠한 에러도 발생하지 않았을 때 반환되는 상태값이다
(2) 'rejected': promise가 거절되었을때 (rejected),
무엇인가 잘못되었을 때 반환되는 상태값이다
(3) 'pending': 아직 promise가 resolved 나 rejected가 아닌 상태

promise 생성자 함수는 콜백을 인자로 받는데 그 콜백함수는 2개의 인자를
받는다. 첫번째로 전달되는 인자는 promise가 resolve되어야 될때 호출되는
res(resolve), 두번째 인자는 promise가 reject 되어야 될때 호출되는
rej(reject)이다.

new Promise((res, rej) => {res('okay!!')})
[[PromiseState]]: 'fulfilled'
[[PromiseResult]]: 'okay!!'

3. [[PromiseResult]]
resolve나 reject 메소드에 전달되는 인자이다.
```

```jsx
promise 객체의 전체를 신경쓰지 않고 promise 값에 내장된 메소드를
사용하여 코드를 작성할 수 있다.
(1) then()
promise가 resolved 후에 호출된다
resolve 메소드에 전달되는 값을 받는다.
(2) catch()
promise가 rejected 후에 호출된다
rejected 메소드에 전달되는 값을 받는다.
(3) finally()
resolved 또는 rejected 후에 항상 호출된다.
```

### `Async/Await`

`Promise` 를 더 쉽게 작성하기 위해 ES7에서 도입된 문법이다. promise 생성자 함수를 사용하지 않고 비동기적으로 처리하길 원하는 함수 앞에 `async` 키워드를 붙이기만 하면 해당 함수는 promise 객체를 반환하게 된다.

```jsx
Promise.resolve("Hi");

async function greet() {
  return "Hi";
}
```
