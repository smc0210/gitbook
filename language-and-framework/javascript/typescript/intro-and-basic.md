---
description: Introduction
---

# Intro & Basic

## 1. Introduce

\(나중에..\)

## 2. Features

### 2-1. Static Type

`javascript`의 장점이자 단점이기도 한 동적 타이핑에 의한 오류를 사전에 검출하고 파악할 수 있다.

```javascript
function sum(a, b) {
    return a + b
}

sum(1,2); // 3
sum('1', '2'); // '12'
```

위와 같은 함수를 작성했다면 그 의도는 `number` 타입 인수를 전달받아서 더한 값을 리턴하는 거지만 아래의 선언부처럼 `string` 타입을 인자값으로 전달하더라도 오류 없이 실행되며 값을 리턴해준다. 그리고 그 리턴 값은 작성자의 의도와는 다른 결과값이고 잡기도 힘들고 에러를 유발하는 원인이 되기도 한다.

이를 타입스크립트의 정적타입을 이용해 작성한다면 컴파일 단계에서 오류를 발견할 수 있다.

```javascript
function sum(a: number, b: number){
    return a + b;
}

sum('1','2'); // error message
```

### 2-2. Support

새로운 언어나 기술을, 하다 못해 라이브러리를 하나 추가하더라도 현업에서는 섣불리 도입을 할 수 없는 가장 큰 이유 중 하나는 아마 지원여부 일 것이다. 팀에서 사용하는 IDE에서 지원하는지?, 커뮤니티는 얼마나 활성화 되어있는지 등등.. 바퀴를 만들기 싫어서 \(시간, 비용 등등...\) 만들어진 바퀴를 사용하는데 그 바퀴에 오류가 있고 그걸 내가 고쳐야 한다면?

반면 타입스크립트는 공식적으로 Microsoft의 지원을 받고 있고, 현존하는 거의 모든 텍스트에디터, IDE등에서도 지원을 한다. 작년 초에는 Google도 내부적으로 사용하던 AtScript가 있음에도 타입스크립트를 공식적으로 사내표준언어 중 하나로 채택했다.

참고용 구글의 사내표준 언어

{% hint style="info" %}
C, C++, Java, Javascript, Python, Go...
{% endhint %}

### 2-3. ES6 / ES Next

ES6만 하더라도 모던 브라우져에서는 거의 지원을 한지 오래지만 아직도 몇몇 브라우져에서는 완벽한 지원이 안되서 `Babel`을 이용하는등 별도의 변환 과정이 필요하다. 이에 반해 타입스크립트는 역시 변환과정이 필요하긴 매한가지지만 babel등의 사용에 비하면 개발환경 구축에 드는 수고가 상대적으로 적은 편이다.

```bash
tsc index.ts // done
```

## 3. Development Environment

개발환경 구축이 간단하다고는 해도 브라우저에서 `.ts`파일이 동작 하지는 않으므로 컴파일러를 이용해 자바스크립트 파일로 트랜스파일링을 해야한다.

### 3-1. install

우선적으로 `NPM`, `Yarn` 등의 자바스크립트 패키지 매니저가 필요하고 이를 위해서 `Node.js`가 필요하다

`Node.js` 설치는 생략

[공식문서](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)의 설치가이드를 보면 npm을 통해 설치하는 가이드만 있는데 `yarn`으로도 가능하다 어떤 패키지 매니저로든 전역적으로 설치를 해준다

```bash
npm install -g typescript

yarn global add typescript

tsc -v  // Version 3.0.1
```

### 3-2. Transpiling

