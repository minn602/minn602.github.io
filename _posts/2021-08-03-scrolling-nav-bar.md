---
layout: post
title: "스크롤에 따라 반응하는 네브바 만들기"
author: Minsun
categories: [Development]
tags: [React, CSS]
image: "https://images.unsplash.com/photo-1558464660-547f0c1f118e?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1049&q=80"
beforetoc: ""
toc: true
---

> 이번 진행한 프로젝트에선 크게 메인 네브, 콜렉션의 서브콜렉션 네브, 상품 상세페이지에서 메뉴탭에서 스크롤 반응에 따라 `height`가 조절되는 네브를 가지고 있다. 메인 네브는 `height`만이 조절되지만 서브콜렉션 네브와 상세페이지의 메뉴탭은 스크롤이 일어날때 상단 네브에 붙고 다시 자신의 위치에선 떨어져야 되었다.

### 생각해낸 방법

처음 서브콜렉션 네브를 맡아서 구현하고자 할때에는 분기를 1. 스크롤 다운되었을 때 + 해당 요소의 위치를 만났을때 2. 스크롤 업 했을 때 3. 스크롤 업하다가 자신의 위치를 찾았을 때 이렇게 3가지 경우로 생각하여 자바스크립트로 만으로 구현하고자하여 코드가 아주 복잡해지고 현재 앱에서 구현된 모바일, 태블릿, 데스크탑 분기로 미디어 쿼리를 자바스크립트로 나눠주다 보니 리사이즈 이벤트가 일어날 경우 버벅이고 전체적으로 앱 성능이 느려지는 느낌이 났었다.

```jsx
//subcollection nav 구현할 때 작성한 코드
useEffect(() => {
  if (navRef && navRef.current !== null) {
    const hadleScroll = throttle(() => {
      const topArea = document.querySelector < HTMLDivElement > ".appTopArea";
      const header = document.querySelector < HTMLDivElement > ".headerArea";

      const navDt =
        document.querySelector <
        HTMLDivElement >
        ".collectionNavigationDtPosition";

      if (
        header &&
        header.scrollHeight &&
        header.offsetHeight &&
        topArea &&
        navRef &&
        navRef.current
      ) {
        if (
          pageY < window.scrollY &&
          // window.scrollY + header.offsetHeight >
          //     navRef.current.offsetTop + 30
          // navRef.current.offsetTop > originPosition)
          // window.scrollY + header.offsetHeight >
          //     document
          //         .querySelector('#subCollectionNav')
          //         .getBoundingClientRect().top
          (header.offsetHeight >
            document.querySelector("#subCollectionNav").getBoundingClientRect()
              .top ||
            document.querySelector("#navPosition").getBoundingClientRect()
              .top !==
              document
                .querySelector("#subCollectionNav")
                .getBoundingClientRect().top)
        ) {
          if (innerWidth < 768) {
            navRef.current.style.paddingLeft = "32px";
            navRef.current.style.marginLeft = "-32px";
            navRef.current.style.borderTop = "1px solid #ebebeb";
            navRef.current.style.boxShadow = "0 2px 5px 0 rgba(0, 0, 0, 0.1)";
            navRef.current.style.top = `${
              topArea.clientHeight - header.clientHeight
            }px`;
            navDt.style.width = "unset";
            navDt.style.maxWidth = "unset";
            navDt.style.margin = "0";
            navDt.style.padding = "0";
          } else if (innerWidth >= 768 && innerWidth < 1200) {
            navRef.current.style.paddingLeft = "64px";
            navRef.current.style.marginLeft = "-64px";
            navRef.current.style.borderTop = "1px solid #ebebeb";
            navRef.current.style.boxShadow = "0 2px 5px 0 rgba(0, 0, 0, 0.1)";
            navRef.current.style.top = `${
              topArea.clientHeight - header.clientHeight
            }px`;
            navDt.style.width = "unset";
            navDt.style.maxWidth = "unset";
            navDt.style.margin = "0";
            navDt.style.padding = "0";
          } else if (innerWidth >= 1200) {
            navRef.current.style.paddingLeft = "0";
            navRef.current.style.marginLeft = `${-navDt.offsetLeft - 64}px`;
            navRef.current.style.borderTop = "none";
            navRef.current.style.boxShadow = "0 2px 5px 0 rgba(0, 0, 0, 0.1)";
            navRef.current.style.top = `${
              topArea.clientHeight - header.clientHeight
            }px`;
            navDt.style.width = "100%";
            navDt.style.maxWidth = "1328px";
            navDt.style.padding = "0 64px";
            navDt.style.margin = "0 auto";
          }

          console.log("11");
        } else if (
          pageY > window.scrollY &&
          // navRef.current.offsetTop !== originPosition
          document.querySelector("#navPosition").getBoundingClientRect().top !==
            document.querySelector("#subCollectionNav").getBoundingClientRect()
              .top
        ) {
          console.log("22", topArea.clientHeight);

          if (innerWidth < 768) {
            navRef.current.style.paddingLeft = "32px";
            navRef.current.style.marginLeft = "-32px";
            navRef.current.style.borderTop = "1px solid #ebebeb";
            navRef.current.style.boxShadow = "0 2px 5px 0 rgba(0, 0, 0, 0.1)";
            navRef.current.style.top = `132px`;
            navDt.style.width = "unset";
            navDt.style.maxWidth = "unset";
            navDt.style.margin = "0";
            navDt.style.padding = "0";
          } else if (innerWidth >= 768 && innerWidth < 1200) {
            navRef.current.style.paddingLeft = "64px";
            navRef.current.style.marginLeft = "-64px";
            navRef.current.style.borderTop = "1px solid #ebebeb";
            navRef.current.style.boxShadow = "0 2px 5px 0 rgba(0, 0, 0, 0.1)";
            navRef.current.style.top = `132px`;
            navDt.style.width = "unset";
            navDt.style.maxWidth = "unset";
            navDt.style.margin = "0";
            navDt.style.padding = "0";
          } else if (innerWidth >= 1200) {
            navRef.current.style.paddingLeft = "0";
            navRef.current.style.marginLeft = `${-navDt.offsetLeft - 64}px`;
            navRef.current.style.borderTop = "none";
            navRef.current.style.boxShadow = "0 2px 5px 0 rgba(0, 0, 0, 0.1)";
            navRef.current.style.top = `185px`;
            navDt.style.width = "100%";
            navDt.style.maxWidth = "1328px";
            navDt.style.margin = "0 auto";
            navDt.style.padding = "0 64px";
          }
        } else if (
          // navRef.current.offsetTop <=
          // originPosition + 30
          document.querySelector("#navPosition").getBoundingClientRect().top ===
          document.querySelector("#subCollectionNav").getBoundingClientRect()
            .top
        ) {
          console.log("33");

          if (innerWidth < 768) {
            navRef.current.style.paddingLeft = "32px";
            navRef.current.style.marginLeft = "-32px";
            navRef.current.style.borderTop = "none";
            navRef.current.style.boxShadow = "none";
            navRef.current.style.top = `0`;
            navDt.style.width = "unset";
            navDt.style.maxWidth = "unset";
            navDt.style.margin = "0";
            navDt.style.padding = "0";
          } else if (innerWidth >= 768 && innerWidth < 1200) {
            navRef.current.style.paddingLeft = "64px";
            navRef.current.style.marginLeft = "-64px";
            navRef.current.style.borderTop = "none";
            navRef.current.style.boxShadow = "none";
            navRef.current.style.top = `0`;
            navDt.style.width = "unset";
            navDt.style.maxWidth = "unset";
            navDt.style.margin = "0";
            navDt.style.padding = "0";
          } else if (innerWidth >= 1200) {
            navRef.current.style.marginLeft = `0px`;
            navRef.current.style.borderTop = "none";
            navRef.current.style.boxShadow = "none";
            navRef.current.style.top = `0`;
            navRef.current.style.paddingLeft = "0";
            navDt.style.width = "unset";
            navDt.style.maxWidth = "unset";
            navDt.style.margin = "0";
            navDt.style.padding = "0";
          }
        }
      }

      pageY = window.pageYOffset;
    }, 200);

    const getInnerWidth = () => {
      setInnerWidth(window.innerWidth);
    };

    document.addEventListener("scroll", hadleScroll);
    window.addEventListener("resize", getInnerWidth);
    return () => {
      document.removeEventListener("scroll", hadleScroll);
      window.removeEventListener("resize", getInnerWidth);
    };
  }
}, [collectionData.contents.subCollections.length, navRef, innerWidth]);
```

### 개선된 방법

`position: sticky`를 이전에도 사용하고 있었지만 이러한 네브를 구현할 때 sticky로 구현이 가능한지 바로 생각해 내질 못했었다. 메인 네브에선 스크롤 반응에 따라 높이를 조절해주었고 그에따라 서브콜렉션 네브와 상세페이지 메뉴탭은 상단 네브의 높이에 따라 `top` 값만 조절하여 주면 간단하게 해결되는 문제였다.

### `position:sticky;` 잘 이해하기

sticky는 relative와 fixed 특성이 조합된 것으로 top, left, bottom, right 속성을 사용하여 위치를 조정해 주세 되어 스크롤 하다 sticky가 적용된 요소를 만나면 설정된 위치 값 만큼 떨어져서 부모 요소에 붙게 된다. sticky를 사용하기 전에는 몇가지 확인해줘야할 사항이 있다

1. 위치 값 추가

   ```scss
   //top 80px 떨어져서 부모요소에 붙게된다
   .child {
     position: sticky;
     top: 80px;
   }
   ```

2. 크로스 브라우징

   ```scss
   .sticky {
     position: -webkit-sticky;
     position: sticky;
     top: 0;
   }
   ```

3. 부모 또는 조상 요소에 overflow 설정되어 있는지 확인<br />
   사용하고자 하는 페이지 콘솔에 아래 코드를 실행시켜보면 간단하게 확인가능하다!

   ```jsx
   //sticky element selector에 sticky 속성을 사용하고자하는 엘리먼트의 selector 설정
   let parent = document.querySelector(
     "[sticky element selector]"
   ).parentElement;

   while (parent) {
     const hasOverflow = getComputedStyle(parent).overflow;
     if (hasOverflow !== "visible") {
       console.log(hasOverflow, parent);
     }
     parent = parent.parentElement;
   }
   ```

4. 부모 요소의 height가 설정되어 있는지 확인<br />
   부모 요소의 height 영역 내에서 sticky 되는 요소가 움직이고 붙기 때문에 꼭 설정해줘야한다. 퍼센트로 설정하는 경우는 동작하지 않는다.

   ```css
   올바른 예

   height: auto
   height: unset
   height: 100vh
   height: 1000px
   height: 5em
   ```

### sticky를 기반으로 작성한 코드

```jsx
//헤더 요소(stciky될 요소)를 ref를 사용하여 파악한다 (jsx에 추가)
const headerRef = useRef(null);

useEffect(() => {
  //스크롤 다운될때와 안될때의 조건에 맞게 top값을 조절해준다
  if (scrollDown) {
    headerRef.current.style.top = "";
  } else {
    headerRef.current.style.top = "";
  }

  window.addEventListener("scroll", scrollHandler);
  return () => {
    window.removeEventListener("scroll", scrollHandler);
  };
}, []);
```

### 스크롤 이벤트를 처리할때 주의점

스크롤 이벤트는 많은 리소스를 잡아먹는 이벤트이다. 이러한 이벤트를 걸어서 돔 요소를 조작하게 되고 그 값을 받아 처리하게 되면 성능적으로 매우 좋지 않다. 이러한 점을 해결하기 위해 도입된 것이 `Throttle` 과 `Debounce` 이다. 무거운 연산을 처리하는 이벤트를 걸어 줄 경우 제어할 수 있는 수준으로 제약을 걸어 이벤트를 발생시켜주는 함수이다.

- debounce<br />
  이벤트를 그룹화하여 특정 시간이 지난 후 하나의 이벤트만 발생하도록 하는 기술<br />
  onChange 이벤트에 따라 api 호출이 필요한 경우 사용하는 것이 적합하다.

- throttle<br />
  이벤트를 일정한 주기마다 발생하도록 하는 기술이다. 설정시간을 1s로 설정하게 되면 1s 동안 최대 한번만 발생하게 된다.<br />
  인피니트 스크롤 구현시에 사용하는 것이 적합하다.

```jsx
useEffect(() => {
  const throttleHandler = throttle(() => {
    scrollHandler();
  }, 500);

  window.addEventListener("scroll", throttleHandler);
  return () => {
    window.removeEventListener("scroll", throttleHandler);
  };
}, []);
```

### Reference

[[HTML/CSS] Position sticky 적용 방법](https://deeplify.dev/front-end/markup/position-sticky)<br />
[디바운스(Debounce)와 스로틀(Throttle ) 그리고 차이점](https://webclub.tistory.com/607)
