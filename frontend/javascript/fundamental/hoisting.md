## Hoisting

`호이스팅(Hoisting)`은 자바스크립트가 **코드를 실행하기 전 변수와 함수 선언을 코드 최상단으로 끌어올리는 것처럼 동작**하는 특성이다. 더 정확하게 말하자면 **스코프 내부 어디서든** 변수와 함수 선언이 최상위에서 선언된 것처럼 동작하는 것이다. (호이스팅은 스코프 단위로 동작한다) 이 때문에 코드의 선언된 위치보다 상단에서 변수에 접근할 수 있는 것처럼 보일 수 있다. 이는 자바스크립트 엔진이 코드를 실행하기 전 변수와 함수 선언을 메모리에 저장하는 방식으로 인해 발생한다.

<br>

## 변수 Hoisting

변수 호이스팅은 `var` `let` `const` 키워드를 사용할 때 각각 다르게 동작한다.

### `var`

**변수 생성 과정**

1. 선언 + 초기화를 동시에 진행

   `var`는 변수를 선언하는 즉시 `undefined`로 값이 초기화된다.

2. 할당

<br>

`var`로 선언된 변수는 **선언과 초기화**는 끌어올려지지만, 값 **할당**은 끌어올려지지 않기 때문에, 값 할당이 이루어지기 전까지는 `undefined`로 평가된다.

```js
var myVar; // [hoisting] 마치 끌어올려 선언된 것처럼 동작

console.log(myVar); // undefined

var myVar = 10; // [실제 코드]
console.log(myVar); // 10 할당
```

<br>

### TDZ

하지만, ES6에 도입된 `let`과 `const`는 선언문 이전에 접근하려고 하면 `ReferenceError`가 발생한다.
이는 `Temporal Dead Zone(TDZ)`때문이다.

`TDZ`란 변수 선언 전 접근 시 에러가 나는 구간을 말한다. `let`과 `const`로 선언된 변수에는 `TDZ`가 존재하며, 이 구간에서는 변수에 접근할 경우 `ReferenceError`가 발생하는 것이다.

![](https://velog.velcdn.com/images/vlmbuyd/post/33a8b1cb-9bb6-4a39-a95b-fc9d6774e5fd/image.png)

<br>

따라서 `let`과 `const`는 값을 초기화(할당) 하기 전에는 사용할 수 없다.

### `let`

**변수 생성 과정**

1. 선언 (Declaration)

   - 변수 이름을 스코프에 등록
   - 메모리에 변수 공간을 마련하지만 아직 값을 넣지는 않음
   - 이 상태에서 변수를 참조하면 `ReferenceError` 발생 (TDZ)

2. 초기화
   - 해당 선언문 `let name = ...`이 실제 실행될 때 초기화 진행
   - 초기화 시점에만 접근 가능 (TDZ 구간 종료)
3. 할당
   - 초기화 후 값을 대입하는 단계
   - `let`은 초기화와 할당을 동시에 할 수도 있고, 나중에 따로 할당할 수도 있음

<br>

```js
console.log(name); // ReferenceError (TDZ 구간)

let name = "Mike";
```

`let` 변수는 선언 자체는 호이스팅되지만 초기화는 호이스팅되지 않는다. 즉, 변수 이름 자체는 미리 인식(호이스팅)하지만, 값을 넣어주기 전까지는 사용할 수 있도록 잠겨있는 것이다. <br>
그리고 이 "잠겨 있는 구간"을 `TDZ`라고 부른다.

<br>

### `const`

**변수 생성 과정**

1. 선언 + 초기화 + 할당 동시에 진행

   - 선언
     - 변수 이름 스코프에 등록
     - 메모리에 공간을 마련하지만 초기화 전에는 접근 불가 (TDZ)
   - 초기화 + 할당 동시에
     - `const`는 선언과 동시에 초기화가 반드시 이루어져야 하므로, 초기화와 할당이 분리될 수 없음
     - 초기값을 할당하지 않으면 `SyntaxError` 발생

   ```js
   console.log(b); // ❌ ReferenceError (TDZ)
   const b = 20; // 선언 + 초기화 + 할당
   ```

   ```js
    const c; // ❌ SyntaxError: Missing initializer
   ```

<br>

### `var` &nbsp; vs &nbsp; `let` &nbsp; vs &nbsp; `const`

| 구분            | var                               | let                              | const                            |
| --------------- | --------------------------------- | -------------------------------- | -------------------------------- |
| **스코프**      | 함수 스코프                       | 블록 스코프                      | 블록 스코프                      |
| **호이스팅**    | 선언 + 초기화(`undefined`) 동시에 | 선언만, 초기화 X (TDZ 구간 존재) | 선언만, 초기화 X (TDZ 구간 존재) |
| **초기화 시점** | 코드 실행 전                      | 선언문 도달 시                   | 선언문 도달 시                   |
| **재선언**      | O                                 | X                                | X                                |
| **재할당**      | O                                 | O                                | X                                |
| **초기값 필수** | X                                 | X                                | O (선언 + 초기화)                |

<br>

## 함수 Hoisting

**함수 선언문**의 경우, 호이스팅으로 인해 코드의 어느 위치에서든 함수를 호출할 수 있다. <br>
하지만, **함수 표현식**은 변수 호이스팅의 규칙을 따르기 때문에, 선언 전에 호출하려고 하면 참조 에러가 발생한다.

```js
console.log(sum(5, 3)); // 8
// 함수 선언문
function sum(a, b) {
  return a + b;
}

console.log(multiply(5, 3)); // TypeError: multiply is not a function
// 함수 표현식
var multiply = function (a, b) {
  return a * b;
};
```

<br>

## 정리

**호이스팅**은 변수와 함수 선언을 코드 상단으로 끌어올리는 것처럼 동작하는 특징을 가리킨다. `var`는 초기화 전에 접근 시 `undefined`로 나타나며, `let`과 `const`는 `TDZ`로 인해 초기화 전에 접근하면 `ReferenceError`를 발생시킨다.

호이스팅으로 인한 혼란을 줄이기 위해,

- **변수**는 코드 최상단에서 선언

- **변수 선언 시** `var` 대신 `let` 또는 `const` 사용

- **함수**는 사용 전에 선언

  함수 선언문은 호이스팅되어 어디서든 호출 가능하긴 하지만, 일관성 있게 사용 전에 선언하는 것이 읽는 사람 입장에서 직관적일 수 있다.

<br>

---

#### 래퍼런스

- https://www.youtube.com/watch?v=ocGc-AmWSnQ

- https://www.maeil-mail.kr/question/31

- https://f-lab.kr/insight/understanding-javascript-hoisting
