---
layout: post
title: "프로젝트 - 장바구니 페이지 후기"
author: Minsun
categories: [Development]
tags: [Developing Story, React]
image: "assets/images/project-cover.png"
beforetoc: ""
toc: true
---

### 글을 시작하며

이번에 프로젝트를 진행하면서 담당하던 페이지 중 하나인 장바구니에 제일 많은 시간을 할애하고 고난도 많이 겪었다. 아직 많은 부분을 개선해야하고 세밀하게 부족한 부분은 많지만 다음 태스크를 시작하기 전에 어떤부분에서 어려웠고 앞으로 다른 페이지에서 반영했으면 좋겠던 부분을 정리하고 넘어가고 싶었다.

### 느낀 점

개발을 시작하며 가장 어려운 구조의 데이터를 프론트단에 붙여본 듯 싶다. 상품에 따라 동일한 데이터구조로 오지 않아 (내 입맛대로) 그거 대로 분기처리를 하고 원하는 기능을 구현할 때에 데이터를 다루는 스킬에 많이 부족함을 느꼈다. 아마 멀티옵션 기능 구현은 먼저 그 기능을 다른 페이지에서 구현하신 다른 개발자분의 팁이 없었다면 정말 시간을 많이 할애했을 것 같다. 주어진 데이터를 잘 다뤄서 처리해야하는 것도 프론트엔드 개발자에겐 정말 필수적인 요소이고 그에 따라 알고리즘 공부를 더 심도 있게 해야겠단 생각이 들었다.

### 새롭게 알게된 점

1. **api 통신시 쿠키 정보 전달하기**<br />
   기존 프로젝트를 진행할 적에는 유저의 로그인 여부를 확인하는 것과 같은 기능을 구현하고자 할때에는 토큰 값을 `localStorage` 에 저장해두고 사용했었는데 이는 XSS 공격에 취약한 방법이라고 한다. 회사에서는 이를 쿠키에 저장하여 api 통신을 할때마다 해당 값을 보내주는 방식을 선택하고 있는데 처음 통신을 할 때에 이 값이 전달되지 않아 많이 애를 먹었다.

   - `Request.credentials` 설정하기<br />
     요청을 보낼때 credentials 속성을 설정하는 값에 따라 cross-origin 요청의 경우 다른 도메인으로 부터 쿠키들을 전달해야되는지 아닌지를 결정하게 된다.<br />
     **credentials: omit** → 절대로 쿠키를 전송하거나 받지 않는다<br />
     **credentials: same-origin** → url이 호출 스크립트와 same-origin이라면 쿠키를 전송한다(기본값)<br />
     **credentials: include** → cross-origin 호출이여도 쿠키를 전송한다

   - 처음 쿠키를 심어주는 동일한 url과 통신하기

2. **반응형 웹 레이아웃**<br />
   반응형웹 레이아웃은 무조건 미디어쿼리에서만 작업을 해줘야한다. 레이아웃이 크게 바뀌는 부분은 현재 width값을 구해서 레이아웃이 바뀌게 하였지만 리사이즈가 일어나는 경우는 리렌더가 일어나지 않고 미디어쿼리를 통해 설정된 스타일만 반영되기 때문에 정확한 반응형웹은 css로만 구현해야한다.

3. **api 호출시 타이밍 맞추기**<br />
   어떠한 버튼을 클릭하게 될 때 여러 api를 호출하게 되는 기능이 많았는데 그와 동시에 스피너도 돌아가게 해야되어서 이러한 타이밍을 처음에는 못맞추었다. 그리하여 api 호출하는 함수들은 호출하고 응답을 리턴하는 함수식으로 바꾸고 그 응답값을 받아서 비동기 식으로 (then method 사용) 타이밍을 맞추었다.

4. **Set**<br />
   받아온 데이터를 중복되지 않는 유니크한 값으로 다시 배열에 담는 것이 필요했는데 이는 자바스크립트의 Set 객체를 사용하면 간단하게 처리가 가능하다.

- `Set Object`<br />
  Set Object 는 값들의 묶음으로 주어진 값들을 반복하며 동일한 값이 존재하는 경우 한번만 담게 되어 유니크한 값을 가지도록 해준다.

  ```jsx
  const arr = [1, 2, 2, 3, 4, 5, 2, 4];
  const obj = new Set(arr);

  console.log(obj);
  // Set (5) {
  //   1, 2, 3, 4, 5
  // }

  const arr2 = [...obj];
  console.log(arr2); //[ 1, 2, 3, 4, 5 ]
  ```