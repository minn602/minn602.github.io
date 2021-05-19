---
layout: post
title: "string data html로 파서하기"
author: Minsun
categories: [Development]
tags: [React]
image:
beforetoc: "string data로 html마크업 태그들이 한꺼번에 전달될 때 리액트에서 파싱하는 방법이다."
toc: true
---

1. dangerouslySetInnerHTML 사용하기

   검색하였을 때 대부분의 방법은 `dangerouslySetInnerHTML` 을 사용하는 것이였다. 하지만 XSS 위험에 노출될 가능성이 커 사용하지 않는 것이 좋다고 하였다.

> dangerouslySetInnerHTML is React’s replacement for using innerHTML in the browser DOM. In general, setting HTML from code is risky because it’s easy to inadvertently expose your users to a cross-site scripting (XSS) attack. So, you can set HTML directly from React, but you have to type out dangerouslySetInnerHTML and pass an object with a \_\_html key, to remind yourself that it’s dangerous.

- XSS 란?

  XSS(Cross Site Scripting): 사이트 간 스크립팅
  사이트 간 스크립팅은 웹 애플리케이션에서 많이 나타나는 취약점의 하나로 웹사이트 관리자가 아닌 이가 웹 페이지에 악성 스크립트를 삽입할 수 있는 취약점이다.

2. 써드 파티 라이브러리 사용하기

   npm에는 html 파싱하기 위한 많은 라이브러리가 있었지만 그것들도 XSS로 부터 안전하지 않기 때문에 사용하지 않았다.

3. DOMPurify

   라이브러리로 HTML을 소독하고 XSS 공격을 예방한다.

4. 함수로 사용

   문자열 데이터를 파싱한 돔 노드로 파싱한 후에 속성들 형식을 자바스크립트에 맞게 고치고 `React.createElement` 함수를 사용하여 리액트 요소로 생성하도록 하였다. 화면에는 잘 렌더되지만 스타일 태그내에 있는 내용들을 JSX 형식으로 바꾸기란 너무 까다로웠다.

   스타일 부분만 따로 떼내어 `dangerouslySetInnerHTML` 로 넣어봤지만 인라인 스타일이 적용되지 않아 결국은 문자열 데이터 통으로 `dangerouslySetInnerHTML`을 사용하였다.

```jsx
// get the content of body tag

const getBodyNodes = new DOMParser().parseFromString(
  portfolioContent, //string html data
  "text/html"
).body.childNodes;

const createJSX = (arr) => {
  return arr.map((node) => {
    let attrObj = {};

    const { attributes, localName, childNodes, nodeValue } = node;

    if (attributes) {
      Array.from(attributes).forEach((attr: any) => {
        if (attr.name === "style") {
          let styleAttr = attr.nodeValue.split(";");
          let styleObj = {};
          styleAttr.forEach((attr) => {
            let [key, value] = attr.split(":");
            styleObj[key] = value;
          });

          attrObj[attr.name] = styleObj;
        } else if (attr.name === "srcset") {
          attrObj["srcSet"] = attr.nodeValue;
        } else {
          attrObj[attr.name] = attr.nodeValue;
        }
      });
    }
    return localName
      ? React.createElement(
          localName,
          attrObj,
          childNodes.length > 0
            ? Array.isArray(Array.from(childNodes))
              ? createJSX(Array.from(childNodes))
              : []
            : null
        )
      : nodeValue;
  });
};

const portfolioDetailElements = createJSX(Array.from(getBodyNodes));
```
