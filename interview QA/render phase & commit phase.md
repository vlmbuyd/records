## 질문

> 리액트의 render phase와 commit phase에 대해서 설명해주세요

<br>

리액트의 렌더링 단계는 크게 렌더 단계(Render Phase)와 커밋 단계(Commit Phase)로 나눌 수 있다.

렌더 단계는 변화된 상태나 props에 따라 **어떤 UI가 변경되어야 할지 결정**하는 단계이다. <br>
커밋 단계는 **변화된 UI를 실제 DOM에 반영**하는 단계이다.

![](https://www.moonkorea.dev/_next/image?url=%2Fassets%2Fmarkdown-image%2FReact-%25EB%25A0%258C%25EB%258D%2594%25EB%258B%25A8%25EA%25B3%2584-%25EC%25BB%25A4%25EB%25B0%258B%25EB%258B%25A8%25EA%25B3%2584%2F%25EB%25A0%258C%25EB%258D%2594-%25EC%25BB%25A4%25EB%25B0%258B.png&w=1920&q=100)

React가 클라이언트(브라우저)의 요청을 받고 주문을 가져오는 웨이터라고 가정해보자. <br>
그렇다면 UI를 요청하고 제공하는 세 가지 단계를 다음과 같이 간단히 비유할 수 있다.

1. 렌더링 **트리거** → 웨이터가 손님의 **주문을 주방으로 전달** (Trigger Phase)

2. 컴포넌트 **렌더링** → 주방에서 **주문한 요리를 준비** (Render Phase)

3. DOM에 **커밋** → 완성된 요리를 **테이블에 내놓기** (Commit Phase)

![](https://github-production-user-asset-6210df.s3.amazonaws.com/157789110/476429890-0c61e3ca-5a7b-4130-a862-7c442756c990.png?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=AKIAVCODYLSA53PQK4ZA%2F20250811%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250811T043940Z&X-Amz-Expires=300&X-Amz-Signature=568b0beb311e1d758836a3781c03a2f37d46532eb5352c7f1a0f688a797c0dfe&X-Amz-SignedHeaders=host)

<br>

## 트리거 단계 (Trigger Phase)

> 렌더링 과정을 시작하게 만드는 신호

컴포넌트가 화면에 그려지기 위해서는 특정 액션, 즉 "트리거"가 필요하다. 가령 사용자가 사이트를 방문하든가, 특정 버튼을 클릭하는 것처럼.

컴포넌트 렌더링이 일어나는 데에는 두 가지 이유가 있다.

1. 컴포넌트 초기 렌더링인 경우

2. 컴포넌트의 상태(또는 부모 요소 중 하나)가 업데이트된 경우

### 트리거 단계 - 초기 렌더링

사용자가 처음 사이트에 방문하면 리소스를 서버에 요청하고 앱이 실행된다.

`render()` 메소드를 호출하여 루트 컴포넌트를 화면에 그린다.

```tsx
import Image from "./Image.js";
import { createRoot } from "react-dom/client";

const root = createRoot(document.getElementById("root"));
root.render(<Image />);
```

### 트리거 단계 - 리렌더링

앱 실행 후에 (`setState` 함수나 props 의해) 상태 업데이트가 되면 리렌더링이 된다. <br>
상태 업데이트 함수가 호출되면 리액트는 렌더링 해야 하는 컴포넌트를 업데이트 큐에 입력한다. 이후 React가 이를 **배칭 / 스케줄링**하여 다음 렌더에서 일괄 적용한다.

- 배칭: 여러 업데이트를 한 번에 렌더
- 스케줄링: 어떤 렌더를 먼저 할지 결정

<br>

## 렌더 단계 (Render Phase)

> 변화된 UI를 결정하는 계산 과정

렌더가 "트리거"되면 렌더 단계로 넘어가 DOM에 그려질 요소들을 파악하는 과정을 거친다. 여기서 **`렌더(링)`란 React에서 컴포넌트를 호출하는 것**을 의미한다.

React는 컴포넌트를 호출하고 모든 자식 요소들을 **재귀적**으로 처리하여 트리의 구성 요소를 파악한다.

**재귀적**?

컴포넌트를 호출했을 때 리턴 값으로 컴포넌트가 반환되면 그 반환된 컴포넌트를 다시 호출하는 과정을 의미한다. 중첩된 컴포넌트가 더 이상 없고 React가 화면에 표시되어야 하는 내용을 정확히 알 때까지 이 과정을 지속한다.

- 재귀 단계에서 함수가 연쇄적으로 호출될 때 `JSX`는 `React.createElement()` 함수에 의해 `React element` 객체로 변환된다.

- React는 이 element들을 기반으로 `Fiber Tree`라는 내부 구조를 만들고, 이 트리가 React의 **가상 DOM** 역할을 수행한다.

- 트리를 따라 호출을 반복하다가 최종적으로 더 이상 컴포넌트가 반환되지 않으면 가상 DOM 트리가 그려진다.

<br>

요소를 파악하는 이 렌더 단계 또한 초기 렌더와 리렌더로 구분할 수 있다.

### 렌더 단계 - 초기 렌더

- 초기 렌더에서는 브라우저가 엔트리 파일을 읽으면서 `render()` 메소드를 호출하여 루트 요소부터 파악한다.

```tsx
function App() {
  return (
    <main>
      <h1>hello world</h1>
      <Item />
      <Item />
    </main>
  );
}
function Item() {
  return <div>I am a Child</div>;
}
ReactDOM.createRoot(document.getElementById("root") as HTMLElement).render(
  <App />
);
```

위 코드에서 `render()` 메소드가 호출되면 리액트는 `createElement()`로 `<main>`, `<h1>`, `<div>` 태그명의 React element를 생성한다.

![](https://www.moonkorea.dev/_next/image?url=%2Fassets%2Fmarkdown-image%2FReact-%25EB%25A0%258C%25EB%258D%2594%25EB%258B%25A8%25EA%25B3%2584-%25EC%25BB%25A4%25EB%25B0%258B%25EB%258B%25A8%25EA%25B3%2584%2F%25EC%25B4%2588%25EA%25B8%25B0-%25EB%25A0%258C%25EB%258D%2594.png&w=1200&q=100)

첫 렌더에서는 가상 DOM과 실제 DOM이 동기화(일치)된다.

### 렌더 단계 - 리렌더

리렌더에서는 이전에 생성한 가상 DOM 트리와 새(WIP) 가상 DOM 트리를 비교(Diff)하여 실제 DOM에 반영할 변경 사항들을 파악한다.

![](https://www.moonkorea.dev/_next/image?url=%2Fassets%2Fmarkdown-image%2FReact-%25EB%25A0%258C%25EB%258D%2594%25EB%258B%25A8%25EA%25B3%2584-%25EC%25BB%25A4%25EB%25B0%258B%25EB%258B%25A8%25EA%25B3%2584%2F%25EB%25A6%25AC%25EB%25A0%258C%25EB%258D%2594.png&w=1920&q=100)

**최소한의 변경 사항만 파악**하기 위해 상태 업데이트가 발생한 컴포넌트를 호출하고 새로운 가상 DOM 트리를 만든다. 리액트가 이전 렌더와 다음 렌더의 변화를 비교하는 과정을 **재조정**이라고 한다.

- 컴포넌트 함수 재호출시 → 새 React element가 생성된다.

- 새 element를 기반으로 `WIP Fiber Tree`를 생성한다.

- Diff : 이전 `current Fiber Tree`(현재 화면과 동기화된 가상 DOM)와 새로 만든 `WIp Fiber Tree`를 비교한다.

- 커밋 단계에서 변경된 부분만 실제 DOM에 반영된다.

- 커밋 완료 시 가상 DOM은 `WIP Fiber Tree`로 대체되고 이전 가상 DOM은 가비지 컬렉션 대상이 되어 사라진다.

<br>

렌더 단계에서는 변경할 UI를 계산하고 파악할 뿐, 그 이상의 작업은 수행하지 않는다.

<br>

## 커밋 단계 (Commit Phase)

> 렌더 단계에서 계산된 결과를 실제로 반영

직전 렌더 단계에서 두 가상 DOM 트리 간의 변화를 실제 DOM에 적용하는 단계이다.

- 초기 렌더에서 React는 `appendChild()` DOM API를 사용하여 렌더 단계에서 파악한 모든 DOM 노드들을 실제 DOM에 반영한다.

- 리렌더 시에는 렌더 단계에서 계산된 최소한의 변경 사항들을 DOM에 적용한다.

  여기서 "적용"이라는 것은 DOM 노드들을 새로 생성, 수정 또는 삭제하여 실제 DOM이 가상 DOM과 동일한 상태가 되도록 동기화하는 것을 의미한다.

<br>
<br>

`Paint` : 위의 단계들을 거쳐 React가 DOM을 업데이트한 후 브라우저는 화면을 다시 그린다 .

<br>

---

#### 래퍼런스

- https://ko.react.dev/learn/render-and-commit
- https://www.moonkorea.dev/React-%EB%A0%8C%EB%8D%94%EB%8B%A8%EA%B3%84-%EC%BB%A4%EB%B0%8B%EB%8B%A8%EA%B3%84
- https://klmhyeonwooo.tistory.com/132
- https://www.maeil-mail.kr/question/30
