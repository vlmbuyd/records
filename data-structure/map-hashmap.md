## Map

키-값 쌍을 저장하는 자바스크립트 내장 객체로 객체와 유사하지만,
<br> 키에 다양한 자료형을 허용한다는 점에서 차이가 있다

- 객체의 키는 항상 문자열 또는 심볼로 변환됨
- Map은 모든 자료형을 키로 사용 가능. 객체도 키가 될수 있다!

### 주요 메서드

- `new Map()` – 맵을 만듦
- `map.set(key, value)` – `key`를 이용해 `value`를 저장
- `map.get(key)` – `key`에 해당하는 값을 반환. `key`가 존재하지 않으면 `undefined`를 반환
- `map.has(key)` – `key`가 존재하면 `true`, 존재하지 않으면 `false`를 반환
- `map.delete(key)` – `key`에 해당하는 값을 삭제
- `map.clear()` – 맵 안의 모든 요소를 제거
- `map.size` – 요소의 개수를 반환

  ```js
  const map = new Map();

  map.set("name", "Alice");
  map.set(1, "one");
  map.set(true, "yes");

  console.log(map.get("name")); // "Alice"
  console.log(map.has(1)); // true
  map.delete(true);
  console.log(map.size); // 2
  map.clear();
  console.log(map.size); // 0
  ```

### 반복 작업을 위한 메서드

- `map.keys()` – 각 요소의 키를 모은 반복 가능한(iterable) 객체를 반환
- `map.values()` – 각 요소의 값을 모은 이터러블 객체를 반환
- `map.entries()` – 요소의 [키, 값]을 한 쌍으로 하는 이터러블 객체를 반환. 이 이터러블 객체는 for..of 반복문의 기초로 사용됨
- `map.forEach(callbackFn)` – 각 요소에 대해 콜백함수를 실행

  ```js
  const map = new Map([
    ["name", "Alice"],
    ["age", 30],
    ["isAdmin", true],
  ]);

  // 각 코드는 한 줄씩 줄바꿈 되어 출력됨

  for (const key of map.keys()) {
    console.log(key); // name, age, isAdmin
  }

  for (const value of map.values()) {
    console.log(value); // Alice, 30, true
    // value: 30
  }

  for (const [key, value] of map.entries()) {
    console.log(`${key} → ${value}`); // name → Alice, age → 30, isAdmin → true
  }

  map.forEach((value, key) => {
    console.log(`${key}: ${value}`); // name: Alice, age: 30, isAdmin: true
  });
  ```

> 📚 출처 <br> https://ko.javascript.info/map-set

<br>

## Hash Function

- key를 고정된 길이의 해시로 변환해주는 함수이다.
- 자바스크립트에서 `Map`을 사용할 때는 자동으로 해시 함수가 적용된다.

<br>

**효율적인 해시함수를 위한 조건**

- 빠른 연산속도 : O(1)의 시간 복잡도로 동작해야 한다.
- 키의 균일한 분포 : 해시 테이블에 균일하게 분포되는 키를 생성해야 한다.
- 입력 키의 모든 문자(또는 비트) 고려 : 키의 일부분에만 의존하지 않고 키의 모든 비트를 사용하여 해시값을 생성해야 한다.
- 충돌 최소화 : 해시 테이블에 배치 시에 충돌 횟수를 최소화해야 한다.

<br>

**해시 충돌**

해시맵은 뛰어난 성능을 발휘하는 자료구조는 맞지만, 모든 경우에서 사용하지는 못한다. 그 이유는 `해시 충돌` 때문이다.
![해시 충돌](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2F73Ch3%2FbtrwbgdQqmL%2FAAAAAAAAAAAAAAAAAAAAAGqZdVqedKthuv009LhvYCbp_BuWIyJjUEPOG3Q7QNus%2Fimg.png%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1751295599%26allow_ip%3D%26allow_referer%3D%26signature%3Ds%252FNo2J07gqmWPs0lcO47GDryKAo%253D)

**해시 충돌**이란 해시 함수를 통해 각각의 다른 key가 중복되는 해시값을 갖게되는 것을 말한다.
이를, 해시 함수 내부 알고리즘으로 해소할 수 있지만 한계가 있기에 보편적으로 다음 두 가지 방법을 사용하여 해시 충돌 문제를 해결한다.

- 분리연결법 (Seperate Chaining)
  ![분리연결법](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2FrowhT%2Fbtrwb4KY6P6%2FAAAAAAAAAAAAAAAAAAAAAMm7Gfx6PzotyKgNv1mcsCJ50Tg_ZbG3fUR7bR8jTiSs%2Fimg.png%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1751295599%26allow_ip%3D%26allow_referer%3D%26signature%3DDs8nnaarlJKeP6El830dr5El1P4%253D)

  같은 해시 값을 갖는 여러 데이터를 하나의 버킷에 저장할 때, 각 데이터를 **연결 리스트** 형태로 이어서 저장하는 방식이다. 즉, 하나의 인덱스에 여러 개의 데이터를 체이닝 방식으로 연결해 충돌을 해결한다.

  - 장점

    - 구현이 간단하고 충돌이 많아도 안정적으로 동작함
    - 버킷에 저장할 수 있는 데이터 수에 제한이 없음

  - 단점
    - 추가적인 메모리(포인터 등)가 필요
    - 연결 리스트가 길어질 경우 성능 저하 우려

- 개방 주소법 (Open Addressing)

  ![개방 주소법](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2FGMsIw%2FbtrwahSsC6W%2FAAAAAAAAAAAAAAAAAAAAAPOSBd_9tJKPL9pJ03oDufUJSLK3rvcNqOCdOzFeU20w%2Fimg.png%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1751295599%26allow_ip%3D%26allow_referer%3D%26signature%3DO5hxVjHSRCRVyhr0xS%252FxeJ7heT8%253D)

  해시 충돌이 발생했을 때, 추가적인 연결 리스트나 메모리 공간을 사용하지 않고, **해시 테이블 내부의 다른 빈 공간을 찾아 데이터를 저장하는 방식**이다.
  버킷이 이미 점유되어 있다면, 일정한 규칙에 따라 다음 인덱스를 탐색하면서 비어 있는 위치를 찾는다.

  즉, 하나의 버킷에는 오직 하나의 요소만 저장되며, 해시값 충돌 시 다른 위치를 찾아 저장한다.

  - 장점

    - 연결 리스트 등 외부 구조 없이 해시 테이블 내의 공간만 활용하므로 추가 메모리가 불필요하다.
    - 배열 기반이라 메모리 접근 속도가 빠르다.

  - 단점
    - 연속적으로 충돌된 데이터가 물려있는 구간을 형성하는 클러스터링 현상이 발생할 수 있다.

> 📚 출처 <br> - https://www.geeksforgeeks.org/what-are-hash-functions-and-how-to-choose-a-good-hash-function <br> - https://joooahn.tistory.com/19 <br> - https://xtozero.github.io/data%20structure/2020/02/16/why-we-use-prime-number-of-buckets.html

<br>

## HashMap

`HashMap`은 키(key)와 값(value)의 쌍을 저장하는 자료구조다. <br>
각 키는 고유하며, 키를 사용하여 해당하는 값을 빠르게 찾을 수 있다.

**어떻게 찾을 수 있을까?**

**`해싱(Hashing)`**

**데이터(주로 키값)를 해시 함수에 넣어 고정 길이의 해시값(숫자)으로 변환하는 과정(행위)** 를 말한다.

**`해시(Hash)`**

- 해싱 과정을 거쳐 얻어진 결과값, 즉 해시값 또는 해시코드를 말한다.
- 변환된 해시코드는 해시 테이블의 배열의 각 요소인 `버킷`(bucket, 저장소)의 **인덱스 역할**을 수행한다.

![해싱의 원리](https://velog.velcdn.com/images/cchoijjinyoung/post/b9e37cc3-34dc-40ef-a1f3-7135fb2b0c3f/image.png)

**버킷 사이즈란?**

해시 테이블 내부에서 사용하는 배열의 크기, 즉 총 버킷의 개수를 의미한다.

![해시맵의 예시](https://velog.velcdn.com/images/cchoijjinyoung/post/77f79456-b4ea-42b5-b404-35f57e09c8f6/image.png)

위의 표와 같이 **해시 값을 인덱스로 하여 데이터(값)를 저장하는 자료구조**를 `해시 테이블`이라고 하고, <br>
이러한 **해시 테이블을 구현한 구체적인 자료구조**를 `해시맵`이라고 한다. <br>

자바스크립트에서 명시적인 해시맵 클래스가 존재하지는 않지만, `Map` `Object` `Set`이 사실상 해시맵의 역할을 수행한다.

해시 테이블은 키를 저장할 때 "순서"가 아닌 "빠른 접근성"을 목표로 하기 때문에 해시 테이블의 키는 순서를 갖지 않는다.

> 📚 출처 <br> - https://velog.io/@cchoijjinyoung/%EC%9E%90%EB%A3%8C%EA%B5%AC%EC%A1%B0-5-HashMap%ED%95%B4%EC%8B%9C%EB%A7%B5%EC%9D%84-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90 <br> - https://www.youtube.com/watch?v=CAMWrFyYaFM

<br>

## Map vs HashMap

| 항목              | `Map`                                            | `HashMap`                                             |
| ----------------- | ------------------------------------------------ | ----------------------------------------------------- |
| 정의              | 자바스크립트의 내장 키-값 컬렉션 객체            | 해시 테이블을 기반으로 구현된 키-값 저장 자료구조     |
| 내부 구조         | 해시 테이블 기반 (구현 방식은 엔진마다 다름)     | 해시 함수 + 배열 + 충돌 처리 방식 명확                |
| 키 자료형         | 모든 자료형 가능 (문자, 숫자, 객체 등)           | 보통 문자열, 객체 등 (언어에 따라 제한 있음)          |
| 순서 보장         | 입력 순서 유지                                   | 보장되지 않음 (`LinkedHashMap` 등으로 순서 유지 가능) |
| 메서드 구성       | `set`, `get`, `has`, `delete`, `forEach` 등 풍부 | 언어마다 다름 (`put`, `get`, `containsKey` 등)        |
| 자바스크립트 내장 | `Map` 객체 사용                                  | 직접 구현하거나 다른 언어에서 사용                    |
| 충돌 처리 방식    | 엔진 내부에서 자동 처리                          | 체이닝, 오픈 어드레싱 등 명시적 구현 필요             |

### 정리

- Map은 자바스크립트에서 해시맵처럼 동작하는 클래스 기반의 내장 객체
- HashMap은 언어나 자료구조 이론에서 해시테이블을 구현한 일반적인 용어

따라서, **"JS에서 Map이 HashMap 역할을 한다"** 는 말은 **실제로 구현 방식은 다를 수 있지만, 동작 목적은 같다**는 의미이다.
