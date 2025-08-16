### `Strict Mode`

리액트 개발 과정에서 발생할 수 있는 예기치 않은 동작이나 버그를 미리 찾고 해결하는 데에 도움을 주는 `React`의 안전 장치이다.

**루트 컴포넌트에서 래핑하여 전체 앱에 대해서 `Strict Mode`를 활성화**할 수도 있고

```tsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";

const root = createRoot(document.getElementById("root"));
root.render(
  <StrictMode>
    <App />
  </StrictMode>
);
```

**앱의 일부분에서만 `Strict Mode`를 활성화**할 수도 있다.

```tsx
import { StrictMode } from "react";

function App() {
  return (
    <>
      <Header />
      <StrictMode>
        <main>
          <Sidebar />
          <Content />
        </main>
      </StrictMode>
      <Footer />
    </>
  );
}
```

<br>

`Strict Mode`는 크게 아래의 네 가지 버그(문제)를 찾아내는 데 도움을 준다. <br>
그리고 이러한 모든 검사는 **개발 환경 전용에서만 동작**하며, **프로덕션 빌드에는 영향을 미치지 않는다.**<br>
개발 환경에서 두 번씩 실행해봤을 때 문제가 발생하지 않으면, 프로덕션에서도 안전하게 실행된다는 신호로 볼수 있다.

### 1. 순수하지 않은 함수(컴포넌트) 감지

`React`는 기본적으로 **순수한** 함수(컴포넌트)를 지향한다. 이것은 두 가지를 의미한다.

- 함수 실행 과정에서 **외부 상태를 변경하지 않아야** 한다.

  `side effect`가 없어야 한다. 즉, 함수 내부에서 외부 객체나 변수, DOM, 네트워크, 상태 등을 직접 변경하지 않아야 한다.

- **같은 입력(props, state...)값이 주어지면, 항상 같은 결과값(JSX...)을 반환**해야 한다.

이는 예측 가능성을 높이고, 성능을 최적화하며 부수 효과에 대해 의도치 않은 DOM/상태 변경을 방지하는 데에 목적이 있다.

반면, 이 규칙을 위반하는 컴포넌트는 예기치 않게 동작하여 버그를 일으킬 수 있다.

따라서 `Strict Mode`는 순수하지 않은 컴포넌트를 찾아내기 위해 **순수해야만 하는 함수들을 개발 환경에서 두 번 호출**하여 컴포넌트가 순수하게 동작하는지 검사하고 버그를 방지한다.

아래의 항목들이 그 예이다.

- 컴포넌트 함수 본문 (단, 최상위 로직만 해당하며 이벤트 핸들러 내부의 코드는 미포함)

- `useState`, `set` 함수, `useMemo`, 또는 `useReducer`에 전달한 함수

- `constructor`과 같은 일부 클래스 컴포넌트 메소드

<br>

**비순수 컴포넌트 예시 (버그)**

```tsx
import React, { StrictMode } from "react";
import { createRoot } from "react-dom/client";

let externalCounter = 0; // ❌ 컴포넌트 외부의 가변 상태

function ImpureCounter() {
  // ⛔️ 렌더링(함수 호출) 때마다 외부 상태를 변경 → 비순수
  externalCounter++;

  return <div>외부 카운터: {externalCounter}</div>;
}

const root = createRoot(document.getElementById("root")!);
root.render(
  <StrictMode>
    <ImpureCounter />
  </StrictMode>
);
```

- Strict Mode에서는 컴포넌트 함수 본문이 의도적으로 두 번 호출되어 <br>
  첫 번째 호출에서 externalCounter가 1 증가, 두 번째 호출에서 또 1 증가 → 화면에는 2가 보임.

- 같은 입력(Props 없음)인데 호출 횟수에 따라 출력이 달라짐 <br>
  → 비순수라서 Strict Mode가 문제를 드러내 줌.

**순수 컴포넌트 예시**

```tsx
type Props = { value: number };

function PureLabel({ value }: Props) {
  // ✅ 외부 상태 변경 없음, 같은 props → 같은 JSX
  return <div>값: {value}</div>;
}
```

Props만으로 결과가 결정되고, 외부 상태를 건드리지 않으므로 Strict Mode의 이중 호출에도 출력이 변하지 않는다.

<br>

### 2. useEffect 클린업 누락 버그 감지

`useEffect`훅은 컴포넌트 마운트 시 또는 의존성이 변경될 때 실행되는 셋업(setup)코드와 컴포넌트가 언마운트될 때 또는 의존성이 변경되기 직전에 정리하는 역할의 클린업(cleanup) 코드로 구성될 수 있다.

`useEffect`에서 발생하는 버그 중 상당수는 클린업 함수를 누락하여 발생하는 경우가 많다.

`Strict Mode`는 모든 `useEffect`에 대해 개발 환경에서 한 번 더 셋업 + 클린업 사이클을 실행하여 해당 버그를 잡아내는 데에 도움을 준다.

<br>

**예시**

```tsx
function ResizeWatcher_Bug() {
  React.useEffect(() => {
    const onResize = () => console.log("resize!");
    window.addEventListener("resize", onResize);

    // ❌ 클린업 없음!
    // return () => window.removeEventListener("resize", onResize);
  }, []); // 의존성 없어서 한 번만 등록하려던 의도

  return <div>윈도우 리사이즈를 감시합니다</div>;
}
```

- Strict Mode(개발)에서는 마운트 시 셋업 → 클린업 → 다시 셋업을 실행해 클린업 누락을 검증한다.

- 위 코드는 클린업이 없으므로, “다시 셋업”될 때 이벤트 리스너가 중복 등록된다. <br>
  → 리사이즈 한 번에 로그가 2번 찍힘(중복 증가) <br>
  → 메모리 누수/중복 동작을 조기 발견!

<br>

### 3. ref 콜백 클린업 누락 버그 감지

마찬가지로 `ref` 콜백 함수에 대해 클린업 함수의 누락을 감지하여 한 번 더 셋업 + 클린업 사이클을 실행한다.

<br>

### 4. 오래된 라이프사이클 메서드와 deprecated APIs 감지

`<StrictMode>` 트리 내부 컴포넌트 중 더 이상 사용되지 않는 API 중 하나를 사용하는 경우 경고를 표시한다.

<br>

---

#### 래퍼런스

- https://ko.react.dev/reference/react/StrictMode

- https://ko.react.dev/learn/keeping-components-pure

- https://www.maeil-mail.kr/question/21
