---
layout: post
title: "input onChange 이벤트를 딜레이 되게 처리하기"
author: Minsun
categories: [Development]
tags: [React]
image: "https://images.unsplash.com/photo-1601445638532-3c6f6c3aa1d6?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=633&q=80"
beforetoc: ""
toc: true
---

## input onChange 이벤트를 딜레이 되게 처리하기

> 처음에 구현한 장바구니의 수량은 플러스, 마이너스 버튼으로 수량조절이 가능하게 구현이 되었는데 100개 단위로 주문이 들어오는 경우도 있다고 하여 타이핑으로도 수량이 변경되는 구현이 필요하였다. input value를 이벤트로 업데이트해줌과 동시에 api 요청도 필요한 작업이라 평소 input에 이벤트를 걸던 방식과는 달리 새로운 것을 많이 접하게 되어 정리를 해보았다!🌝

### 이벤트가 실행되는 조건

input 이벤트가 실행될 때의 조건으로 우선적으로 생각한 것은 아래와 같다.

1. 처음 렌더시 input value는 장바구니에 담긴 수량을 렌더하게 된다.
2. 수량 입력이 되면 수량 변경 api를 호출한다(입력 될때마다 호출하면 안됨)

### 각 이벤트마다 실행되는 방식

원하는 기능을 수행하기 위해서는 onChange 외 다양한 이벤트를 걸어서 수행할 수 있었다. 각 이벤트 별로 테스를 해본다음 제일 이상적인 이벤트를 적용하였다.

1. `onBlur`<br />
   onBlur 이벤트는 현재 타이핑 되고 있는(포커스 된) 인풋이 포커스를 잃게 되면 실행되는 이벤트로 타이핑 후 다른 곳을 선택하여 포커스가 이동되면 해당 이벤트에 걸린 함수가 실행하게 된다. 타이핑 후 어느정도 딜레이 후에 실행되길 원해서 해당 이벤트는 사용하지 않았다.

2. `onKeyPress`<br />
   onKeyPress 이벤트는 enter key를 입력할 때 함수가 실행되도록 하기 위해 사용하였다. 사용자의 입장에서 항상 인풋을 입력 후 엔터를 치는 버릇이 있고 그럴 때 원하는 동작이 수행되는 것에서 만족감을 느꼈기 때문에 사용하였다.

   ```jsx
   onKeyPress={
   	(evt) => {
   		if(evt.key === 'Enter'){
   			//run here..
   		}
   	}
   }
   ```

3. `onChange`<br />
   onChange 는 인풋의 값이 변화할때 마다 일어나는 이벤트로 기본적으로 인풋 값을 업데이트할때 자주 사용하던 이벤트이다. 여기서 원하는 동작은 타이핑이 일어날때마다 실행되는 것이 아닌 유저가 타이핑을 멈출 때, 실행되는 것으로 어느정도 딜레이 이후 이벤트가 일어나는 작업이 필요했다.

### 딜레이 후에 onChange 이벤트 실행시키는 방법

1. `debounce` 함수<br />
   useEffect을 사용하여 input value가 바뀔때마다 감지하여 setTimeout으로 이벤트가 지연된 후에 실행되도록 처리할 수 있지만 lodash 라이브러리에 있는 debounce 함수를 사용하여 동일한 동작 수행이 가능하다. debounce 함수도 내부적으로 setTimeout를 사용하며 동작되지만 실행하는 횟수를 조절하여 지연 시간 내에 여러 요청이 발생하는 경우 하나만 호출되도록 제어해준다.

```jsx
//lodash 라이브러리에서 debounce 함수 호출 하는 방법
import _, { debounce } from "lodash";

//debounce된 함수는 debounce의 두번째 인자로 전달된 시간 후에 첫번째 인자 함수를 실행하게 된다.
const onChangeHandler = debounce((evt) => {
  console.log(evt.target.value);
}, 1000);
```

1. `useMemo` hooks<br />
   위와 같이 작성하게 되면 세팅한 1초후에 이벤트는 실행하게 되지만 지연시간(1초) 이내에 여러 이벤트를 발생시킬 경우 여러번 실행되는 걸 볼 수 있었다. 이런 문제를 위한 해결방법으로는 useCallback나 useMemo 훅스로 감싸주는 것이다. 이러한 문제가 발생하는 이유는 리액트 컴포넌트 내부에 디바운스 함수를 적용할 때에는 디바운스 된 함수가 컴포넌트 재렌더링 간에 동일하게 유지되어야 되기 때문이다. (재렌더시마다 해당 함수는 새롭게 선언되기 때문에 동일하게 유지되는것이 불가능하다)

   useCallback 은 렌더링이 일어날때마다 debounce로 감싼 함수의 새인스턴스를 생성하게 되므로 이를 피하기 위해 useMemo를 사용하는 것이 좋다.

   ```jsx
   const onChangeHandler = useMemo(
     debounce((evt) => {
       console.log(evt.target.value);
     }, 1000),
     []
   );
   ```

### input 관련 새롭게 알게된 점

input 태그에는 defaultValue, value attribute 가 있는데 defaultValue는 처음 렌더될때 표시되는 값이고 value는 변화에 의해 만들어진 값을 표현하고 싶을 때 사용한다. 재렌더에 의해 데이터가 바뀜에도 defaultValue는 처음 렌더시의 값만 표기하기 때문에 바뀌지 않는다. 재렌더 될때마다 defaultValue도 업데이트 해주고 싶으면 강제로 key 값을 동적으로 설정하여 재렌더의 영향을 받도록(리액트는 key 값에 의해 변화된 가상 돔을 감지하기 때문이다) 조작할 수 있다.

```jsx
//동적으로 key값을 갖기 때문에 리액트는 이부분이 업데이트 되었다고 감지하여 defaultValue도
//재렌더의 영향을 받게 된다.
<input key={Math.random() * 1000} defaultValue={data.quantity} />
```

### References<br />

[ReactJS delay onChange while typing](https://stackoverflow.com/questions/53071774/reactjs-delay-onchange-while-typing)<br />
[Perform debounce in React.js](https://stackoverflow.com/questions/23123138/perform-debounce-in-react-js)<br />
[How to Correctly Debounce and Throttle Callbacks in React](https://dmitripavlutin.com/react-throttle-debounce/)
