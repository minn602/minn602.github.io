---
layout: post
title: "useRef 사용시 발생하는 null 오류 해결과 대응방법(ex: Cannot read property 'style' of null)"
author: Minsun
categories: [Development]
tags: [Developing Story, React]
image: "assets/images/project-cover.png"
beforetoc: ""
toc: true
---

## useRef 사용시 발생하는 null 오류 해결과 대응방법(ex: Cannot read property 'style' of null)

> 많은 페이지 내에서 스크롤링에 따라서 특정 요소에 스타일 값 등을 주고 싶을 때 `useRef`를 사용하여 특정 DOM 요소에 접근하여 조절하고 있다. 해당 페이지 내에선 잘 동작하지만 가끔 `useRef`를 사용한 페이지에서 다른 페이지로 이동을 할때 `Cannot read property 'style' of null` 오류가 발생하는 경우가 있다. 해당 오류를 해결하는 방법을 기록해보았다!

### useRef와 useEffect를 함께 사용할 시 주의해야 할 점.

윈도우 전체에 걸린 스크롤 반응에 따라 특정 요소의 스타일 값이 변화되어야 되기 때문에 `useEffect` 내에서 `useRef` 전달되는 요소에 스타일 값을 주었더니 다른 페이지로 넘어 갈때 `ref` 오류가 발생하는 문제가 있었다.

```jsx
const menuTabRef = useRef(null);

useEffect(() => {
            const scrollHandler = throttle(() => {
             //스크롤 반응에 따라 menuTabRef.current.style.top 에 접근하여
						 //해당 값 조절하는 로직 추가
            }, 500);

            window.addEventListener('scroll', scrollHandler);
            return () => {
                window.removeEventListener('scroll', scrollHandler);
            };
        }
    }, []);
```

위와 같은 상황에서 오류가 일어나는 경우는 **리액트의 라이프사이클**을 살펴보면 된다.<br />
처음에 useRef()라는 함수가 생성이 되고 렌더 후에 useRef()의 값을 받게 된다. 이때 첫 렌더가 이뤄지는 과정에서 딜레이가 생기게 되는 경우가 있는데 돔트리를 그리는 과정을 살펴보면 HTML, CSS, javaScript가 비동기처럼 그려지기 때문이다. 따라서 html, css가 그려지지 않은 상태에서 useRef() 함수가 실행되는 이유는 돔트리 구성과 cssOM트리가 구성되는 과정에서 자바스크립트가 실행되는 **Critical Rendering Path** 방식이기 때문인 것을 알 수 있다. 다시 리액트 라이프사이클을 살펴보면 `constructor → render → componentDidMount` 순으로 실행이 되는데 render(html+css) → useEffect()(js 영역) 순으로 실행이 일어나면 위와 같은 이유로 html, css가 모두 그려지지 않는 상태에서 자바스크립트가 실행되기 때문에 `null` 이라는 결과값을 반환할 수 있다.

### DOM요소를 가리키는 ref.current를 useEffect의 의존성 배열로 넣어도 될까?

ref.current와 같이 수정가능한 값은 useEffect의 의존성 배열에 넣을 수가 없다. 왜냐하면 해당 값들은 재렌더의 트리거가 되지 않기 때문에 useEffect 또한 재렌더 되지 않는다.

### `ref.current`를 사용하던 페이지를 벗어났을 때에 왜 같은 오류가 발생할까?

사실 해당 오류는 ref.current를 적용한 페이지 내에선 잘 동작하다가 다른 페이지를 이동했을 때, (route가 변경되고 아예 다른 컴포넌트가 렌더되어야 될때) 마주친 오류이다. 이전 ref.current를 참조하는 컴포넌트는 언마운트 되기 때문에 해당 오류가 발생하는 것에 의문이 생겼다. 이는 위의 코드에서 scroll 이벤트를 useEffect에서 사용할 때 cleanup 해주는 것처럼 ref값도 cleanup 작업이 필요하다.

### 해결방법

리액트 공식문서에선 콜백 ref를 사용하는 것을 추천한다. ([https://ko.reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node](https://ko.reactjs.org/docs/hooks-faq.html#how-can-i-measure-a-dom-node))

```jsx
//useClientRect라는 커스텀 훅 생성
export const useClientRect = () => {
    const [rect, setRect] = useState(null);
    const ref = useCallback((node) => {
        if (node !== null) {
            setRect(node);
        }
    }, []);
    return [rect, ref];
};

//ref접근이 필요한 컴포넌트 내
const [menuTabRect, menuTabRef] = useClientRect();

useEffect(() => {
        if (menuTabRect !== null) {
	   //스크롤이벤트에 따라 해당 요소의 값을 조절한다.

						window.addEventListener('scroll', scrollHandler);
            return () => {
                window.removeEventListener('scroll', scrollHandler);
            };
        }
    }, [menuTabRect]);

.....

return (
	<div ref={menuTabRef} className="menuTab">
)
```

### References

[https://medium.com/@teh_builder/ref-objects-inside-useeffect-hooks-eb7c15198780](https://medium.com/@teh_builder/ref-objects-inside-useeffect-hooks-eb7c15198780)<br />
[Why you shouldn't put refs in a dependency array](https://epicreact.dev/why-you-shouldnt-put-refs-in-a-dependency-array/)
