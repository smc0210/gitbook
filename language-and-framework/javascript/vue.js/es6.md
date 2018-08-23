# ES6

### 1. ES6?

{% hint style="info" %}
ES6 = (ECMAScript 2015)
{% endhint %}


> ES5

```javascript
var num = 100;
var sumNum = function(a, b) {
    return a + b;
};
sumNum(10, 20); //30
```

> ES6

```javascript
const num = 100;
let sumNum = (a, b) => {
    return a + b;    
};
sumNum(10, 20); //30
```

### 2. Const,let

ES6는 `var`대신 `let`으로 변수를 선언하며
선언한 후 값이 바뀌지 않고 동일하게 사용되는 변수는 `const`를 사용한다 (상수)

```javascript
// let : 할당한 값을 변경할 수 있음
let a = 10;
a = 20; //20

// const : 값의 갱신을 허용하지 않음
const a = 10;
a = 20; // Uncaught TypeError: Assignment to constant variable.
```

### 3. Block Scope

ES6에서는 `let`으로 선언한 변수의 유효범위가 `{}` 안으로 한정된다.

> ES5

```javascript
var i = 10;
for (var i = 0; i < 5; i++) {
    console.log(i); // 0,1,2,3,4
}
console.log(i); //5
```

> ES6

```javascript
let i = 10;
for (let i = 0; i < 5; i++) {
    console.log(i); // 0,1,2,3,4
}
console.log(i); // 10
```

### 4. Arrow Functions

ES5의 함수 정의 방식을 간소화한 문법으로 속도도 더 빠르다

> ES5

```javascript
var sumNum = function (a, b) {
    return a + b;
};
```

> ES6

```javascript
var sumNum = (a, b) => {
    return a + b;
};
```

### 5. Modules

{% hint style="info" %}
ES5 는 모듈화를 지원하는 라이브러리를 사용하거나 프로그래밍 패턴으로 모듈관리를 했었지만,
ES6 부터는 언어자체에서 import와 export로 모듈화를 지원한다.
{% endhint %}

{% hint style="warning" %}
자바스크립트는 변수의 유효범위가 파일단위로 구분되지 않고 기본적으로 같은 유효범위를 갖기 때문에
기존에 정의된 변수를 재정의 하거나 유효범위가 충돌하는 경우를 방지하기 위해 반드시 모듈화가 필요하다.
{% endhint %}



```javascript
// 파일명 예시 : ./app/login.js
export const id = 'test';

// 파일명 예시 : ./main.js
import { id } from './app/login.js';
console.log(id); // test
```
{% hint style="warning" %}
import 대상 파일에 export가 정의되어 있지 않으면 기본적으로 파일의 모든 내용이 export 된다
{% endhint %}


