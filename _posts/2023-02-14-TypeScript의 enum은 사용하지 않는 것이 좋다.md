---
layout: post
title: "TypeScript의 enum은 사용하지 않는 것이 좋다."
author: Minsun
categories: [Development]
tags: [TypeScript]
image: ""
beforetoc: ""
toc: true
---

> 프로젝트에 사용되는 상수를 주로 object 형태나 TypeScript의 enum으로 선언하여 사용하는 것을 선호하였다. enum의 경우 문자열 할당이 가능하여 좀더 명확한 의미를 이해할 수 있고 enum에 들어있는 값만을 사용해야 되기 때문에 개인적으로 enum을 더 선호하는 편이였다. 그러다 상수만을 정리해둔 파일을 정돈하다가 사용하고 있는 enum 형태에 대한 의문이 생겨 리서치를 하게 되었다.
> 

### enum이란?

enum은 열거형 변수로 정수를 하나로 합칠 때 편리한 기능이다. 임의의 숫자나 문자열을 할당할 수 있으며 하나의 유형으로 사용하여 버그를 줄일 수 있다.

```tsx
//아무것도 할당하지 않는 경우 0부터 숫자가 매겨진다.
enum LANGUAGE {
  KO, //0
  US, //1
  JA //2
}

//임의의 숫자나 문자열을 할당할 수 있다.
enum LANGUAGE {
  KO = 'korea',
  US = 'us',
  JA = 'japan'
}
```

### enum을 사용하면 Tree-shaking 되지 않는다.

Tree-shaking이란 사용하지 않는 코들르 삭제하는 기능을 말한다. 나무를 흔들면 죽은 잎사귀들이 떨어지는 모습에서 착안되어 이와 같이 부른다. Tree-shaking 기능을 통하여 export 했지만 아무데서도 import 되어 사용되지 않는 모듈이나 코드를 삭제하여 번들 크기를 줄여 페이지가 표시되는 시간을 단축시킬 수 있다.

enum은 TypeScript 자체적으로 구현되었기 때문에 TypeScript 컴파일러는 IIFE(즉시 실행 함수)를 포함한 코드를 생성한다. 그런데 Rollup과 같은 번들러는 IIFE를 사용하지 않는 코드라고 판단할 수 없어서 Tree-shaking 되지 않는다. 만약 export 되었지만 사용되지 않더라도 최종 번들에는 포함이 된다.

```tsx
//typescript enum
export enum MOBILE_OS {
  IOS,
  ANDROID
}

//위의 타입스크립트 코드를 트랜스파일하면 아래와 같은 자바스크립트 코드가 생성된다.
export var MOBILE_OS;
(function (MOBILE_OS) {
    MOBILE_OS[MOBILE_OS["IOS"] = 0] = "IOS";
    MOBILE_OS[MOBILE_OS["ANDROID"] = 1] = "ANDROID";
})(MOBILE_OS || (MOBILE_OS = {}));
```

### Union Types

위에서 언급된 enum의 문제를 해결하지 위해선 Union Types를 사용하면 된다.

```tsx
//union types를 사용하여 작성된 typescript 코드
const LANGUAGE = {
  KO: 'korea',
  US: 'us',
  JA: 'japan'
} as const;
type LANGUAGE = typeof LANGUAGE[keyof typeof LANGUAGE];

//위의 코드를 트랜스파일된 자바스크립트 코드
const LANGUAGE = {
  KO: 'korea',
  US: 'us',
  JA: 'japan'
}
```

위와 같이 작성하면 TypeScript 코드에선 LANGUAGE에 정의한 타입에 대한 이점을 얻고 자바스크립트로 트랜스 파일해도 IIFE가 생성되지 않아 Tree-shaking을 할 수 있다.

### const enum

TypeScript에서 const enum을 사용하면 enum과 거의 같다고 느낄 수 있다. 하지만 enum의 내용이 트랜스파일할 때 인라인으로 확장된다는 점이 있다. Tree-shaking면에서는 Union Types와 동일하게 사용하지 않으면 번들에 포함되지 않지만 babel로 트랜스파일 할 수 없고 TypeScript의 —isolatedModules가 활성화된 환경에선 큰 의미가 없다.