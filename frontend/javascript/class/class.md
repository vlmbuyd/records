## 클래스

클래스는 인스턴스를 만들기 위한 ‘틀’(템플릿, 설계도)이자 (e.g. 붕어빵 틀) <br>
인스턴스를 만들 줄 아는 특수한 함수 ‘객체’이다.

<br>

## 인스턴스

설계도를 바탕으로 `new` 키워드를 활용해 실제로 메모리에 찍어낸 “실물 객체”이다.

<br>

## `this`

```tsx
class User {
  // (인스턴스) 메서드
  constructor(name) {
    // this는 클래스가 생성할 인스턴스를 가리킨다.
    this.name = name;
  }

  sayHi() {
    alert(this.name);
  }
}

// 인스턴스 생성
let user = new User("John");
user.sayHi(); // Output: John
```

- constructor 함수 내부에서 정의한 this는
  앞으로 생성될 인스턴스 “그 자체”를 가리키는 참조이다.
- `this.name = name` <br>
  “지금 만들고 있는 이 객체(this)에 `name` 이라는 속성을 붙이고,
  파라미터로 받은 `name` 값을 거기에 할당해!

<br>

## `constructor`

`constructor` 는 인스턴스를 생성하고 클래스 필드를 초기화하기 위한 특수한 메서드이다.

<aside>

**클래스 필드(class field)**

클래스 내부의 캡슐화된 변수를 말한다. 데이터 멤버 또는 멤버 변수라고도 부른다.
클래스 필드는 인스턴스의 프로퍼티 또는 정적 프로퍼티가 될 수 있다.
쉽게 말해, 자바스크립트의 생성자 함수에서 this에 추가한 프로퍼티를
클래스 기반 객체지향 언어에서는 클래스 필드라고 부른다.

</aside>

- 인스턴스 생성 시 `new` 연산자 호출 시 `constructor` 가 함께 호출됨
  ⇒ `constructor` 는 **인스턴스의 생성과 동시에 클래스 필드 생성과 초기화 실행**
  ```tsx
  class Foo {
    constructor(name = "") {
      this.name = name; // 클래스 필드의 선언과 초기화
    }
  }
  const foo = new Foo("Lee");
  console.log(foo); // Foo { name: 'Lee' }
  ```
- `constructor` 는 클래스 내에 한 개만 존재 가능
- 생략 가능
  ⇒ 생략 시 `constructor() {}` 를 포함한 것과 동일하게 동작 (빈 객체 생성)

  ```tsx
  class Foo {}

  const foo = new Foo();
  console.log(foo); // Foo {}

  // 프로퍼티 동적 할당 및 초기화
  foo.num = 1;
  console.log(foo); // Foo { num: 1 }
  ```

<br>

## 정적 프로퍼티

- `static` 키워드를 붙여서 선언
- 특정 인스턴스가 아닌 클래스 자체에 공통된 값을 저장 (클래스의 “전역 변수”처럼 동작)
  ⇒ 모든 인스턴스가 ‘공유’하는 값을 저장하고 함께 변경하며 사용

**언제 사용?**

모든 인스턴스가 동일하게 참조해야 하는 값일 때 사용

예시

```tsx
class User {
  static userCount = 0; // 정적 프로퍼티

  constructor(name) {
    this.name = name;
    User.userCount += 1; // 생성자가 호출될 때마다 클래스의 userCount를 1 증가
  }
}

const user1 = new User("Kim"); // +1
const user2 = new User("Park"); // +1

console.log(`지금까지 생성된 유저 수: ${User.userCount}`); // "지금까지 생성된 유저 수: 2"
```

<br>

### `constructor` vs `static property`

| **구분**                           | **`constructor` (생성자)**                | **`static property` (정적 프로퍼티)** |
| ---------------------------------- | ----------------------------------------- | ------------------------------------- |
| **개념**                           | 함수 (기능)                               | 데이터 (값)                           |
| **소속**                           | 클래스 (설계도)의 ‘인스턴스 생성 로직’    | 클래스 자체 (설계도 자체에 귀속)      |
| **목적**                           | “새 인스턴스”를 초기화                    | “클래스 공통”의 값 저장 및 공유       |
| **접근/실행**                      | `new User()`                              |
| → `new` 키워드로 실행              | `User.userCount()`                        |
| → `클래스이름.값`으로 조회 및 수정 |
| **this**                           | 생성될 인스턴스 “그 자체”를 가리키는 참조 | (해당 없음. 그 자체로 값이기 때문에)  |

<br>

## 정적 메서드

- `static` 키워드를 붙여서 선언
- 정적 메서드와 정적 프러퍼티는 생성된 인스턴스와 상관없이 **클래스(설계도) 자체에 직접 소속되어 독립적인 기능 수행**
  ⇒ `new` 로 인스턴스를 만들지 않고 `클래스이름.메서드()` 방식으로 원본 클래스로부터 직접 호출
- 정적 메서드 내부의 `this` 는 인스턴스가 아니라 클래스 자체를 가리킴
  - “인스턴스의 데이터가 필요 없다”
  - 따라서 정적 프로퍼티에느 접근할 수 있지만, 인스턴스에는 접근할 수 없음

**언제 사용?**

인스턴스의 데이터(상태)와는 상관없이, 클래스 레벨에서 수행할 수 있는 기능일 때 사용

(e.g. 유틸리티 / 헬퍼 함수 등)

**예시**

```tsx
// '숫자인지 검증'하는 기능은 특정 자동차(인스턴스)의 데이터가 필요 없습니다.
class Validator {
  static isNumber(input) {
    return typeof input === "number" && !isNaN(input);
  }
}

// new Validator() 할 필요가 없습니다.
const isValid = Validator.isNumber(123); // true
const isInvalid = Validator.isNumber("abc"); // false
```

<br>

### 인스턴스 메서드 vs 정적 메서드

| **기준**      | **일반 메서드 (인스턴스 메서드)** | **정적 메서드 (Static Method)** |
| ------------- | --------------------------------- | ------------------------------- |
| **소속**      | **인스턴스 (객체)**               | **클래스**                      |
| **호출 방식** | `new`로 인스턴스를 **만든 후**    |

`const user1 = new User();
user1.sayHi();` | `new` 없이 **클래스 이름**으로 바로
`Validator.isNumber(123);` |
| **`this`** | 메서드를 호출한 **'그 인스턴스'**
(예: `user1`) | **'클래스 자체'**
(예: `Validator`) |
| **목적** | 인스턴스의 **데이터(속성)를 사용**하거나 **상태를 변경**
(예: `this.name` 읽기, `this.position` 바꾸기) | 인스턴스와 **상관없이** 수행하는 기능
(예: 유틸리티, 헬퍼 함수, 팩토리) |

 <br>

---

#### 래퍼런스

- https://poiemaweb.com/es6-class
