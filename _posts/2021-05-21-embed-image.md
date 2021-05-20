---
layout: post
title: "반응형 웹에 맞는 이미지 넣기"
author: Minsun
categories: [Development]
tags: [CSS]
image:
beforetoc: "반응형 웹에 이미지를 넣을 때 잘리는 부분 없이 레이아웃에 맞춰 이미지를 넣을 수 있는 방법이다"
toc: true
---

1. `img` 태그

   처음엔 당연히 이미지 태그를 사용하여 사이즈, 패딩, 마진을 조절하여 레이아웃을 잡았다. 문제는 미디어 쿼리였다. 이미 많은 양의 사진이 static으로 들어가 있는 상태에서 current width를 구하기 위해 리사이즈 이벤트를 전체에 걸면 너무 무거워질 거 같은 생각이 들었다. 어찌됐건 반응형 웹을 구현하는데에 가장 기본은 미디어 쿼리이기 때문에 백그라운드 이미지로 처리하는게 젤 정확하고 맘 편할거 같은 생각이 들었다.

2. `background` 속성

   @media break-point에 따라 모바일과 데스크탑에서 각각 적용될 사진 경로를 지정해 주었다. 이때 이미지의 사이즈를 결정짓는 건 `background-size`로 이전에 `cover`를 많이 사용했었는데 이미지가 잘리지 않고 전체가 나오기 위해서는 100%로 설정해줘야한다.

   ```scss
   background: url("image path") no-repeat center center;
   background-size: 100%;
   ```

   **이미지의 기본 사이즈?**

   어떠한 요소든지 보여지기 위해서는 가지고 있는 height 값이 있어야 하는데 반응형 웹에서 고정된 width나 height 값을 넣게 되면 뷰포트의 사이즈에 따라 화질이 깨지는 현상이 발생했다. height값을 고정으로 넣지 못하지만 이미지가 출력되기 위해선 있어야하고 이럴 때는 `padding-bottom` 값을 %로 넣어주면된다.

   예를 들어 `width: 264px, height: 358px`의 이미지를 넣는 경우 `padding-bottom`에 (358 / 264) \* 100 = 135% 를 넣어주면 반응하는 사이즈의 이미지가 된다.

   **`padding-bottom`의 문제점**

   `height` 값을 넣지 않고 `padding-bottom` 값으로 이미지의 사이즈를 조절할 수 있지만 만약 이미지가 뷰포트에 꽉차게 나오지 않고 `margin`을 갖고 있는 경우는 기존에 갖고 있는 `padding-bottom` 값에 의해 의도치 않는 여백이 생겨 잡아주던 레이아웃이 망치는 경우가 생긴다. (사진이 잘리지 않고 다보여되기 때문에!)

3. 다시 `img` 태그

   레이아웃에 맞게 잘리지 않고 반응형 웹에 잘 맞게 이미지를 넣기 위해선 img 태그를 사용하는게 적합한거 같다! 지금껏 찾은 방법으론 @media break-point 기준에 맞게 모바일에서 보여질 이미지, 데스크탑에서 보여질 이미지를 display 값으로 조정하는게 젤 깔끔한 것 같다.

   ✨ 더 효율적인 방법 찾아보기!

### Reference

[Responsive height proportional to width](https://stackoverflow.com/questions/11535827/responsive-height-proportional-to-width)

[@media queries and image swapping](https://stackoverflow.com/questions/27853884/media-queries-and-image-swapping/27853917)
