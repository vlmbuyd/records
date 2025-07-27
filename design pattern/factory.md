## 팩토리 패턴

객체를 사용하는 코드에서 객체 생성 부분을 별도의 함수(혹은 클래스)로 분리하여 추상화한 패턴.

<br>

**함수 기반 팩토리 패턴**

직접 `new`로 객체를 생성하지 않고, **팩토리 함수 (혹은 메서드)를 통해 객체 생성**하기 때문에 함수를 호출하는 것으로 객체를 만들어낼 수 있다.

```js
const createUser = ({ firstName, lastName, email }) => ({
  firstName,
  lastName,
  email,
  fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
});
```

**객체를 어떻게 만들지(생성 로직)는 내부에 감추고**, 외부에서 `createUser`를 호출하면 **공통 생성 로직으로 원하는 객체(혹은 인스턴스)를 만드는** 구조이다.

<br>

**클래스 기반 팩토리 패턴**

- 상속 관계에 있는 두 클래스에서 **상위 클래스가 뼈대를 결정**하고, **하위 클래스가 객체 생성에 관한 구체적 내용 결정**

- 객체 생성을 별도의 class나 메서드(주로 static 메서드)에 위임한다.

<br>

### 장점 및 단점

**장점**

- 객체 생성 시 초기화 / 구성 / 유효성 검사 등을 함수 내부에서 처리할 수 있어 생성 로직과 사용 코드를 분리할 수 있다.

- 동일한 프로퍼티를 가진 여러 작은 객체를 만들어낼 때 유용하다.

**단점**

- 자바스크립트에서의 팩토리 함수는 new 키워드 없이 객체를 만드는 것에서 크게 벗어나지 않는다.

- 함수형 팩토리 함수는 간결하고 직관적이지만, 반복적으로 객체를 만들 때 **메서드가 중복 생성**되어 **메모리 낭비가 발생**할 수 있다.

  ```js
  function createUser(name) {
    return {
      name,
      sayHello() {
        return `Hello, ${this.name}`;
      },
    };
  }
  ```

  반면 클래스는 공통 메서드 하나만 만들어서 공유하므로 메모리 효율이 좋아 대량의 객체 생성 시 성능 상 이점이 있다.

  ```js
  class User {
    constructor(name) {
      this.name = name;
    }
    sayHello() {
      return `Hello, ${this.name}`;
    }
  }

  const user1 = new User("Kim");
  const user2 = new User("Lee");
  ```
