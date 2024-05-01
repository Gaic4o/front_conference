[React.js의 렌더링 방식 살펴보기 - 이정환](https://www.youtube.com/watch?v=N7qlk_GQRJU)

# React.js의 렌더링 방식 살펴보기

### 1. 웹 브라우저는 어떻게 동작할까?

브라우저는 HTML과 CSS로 작성된 웹 페이지를 어떤 과정을 거쳐 화면에 렌더링하는지 알아봅시다.

- HTML,CSS -> DOM (HTML 배치 모양 정보), CSSOM (CSS 스타일 요소)
- Render Tree 생성 : DOM과 CSSOM 합쳐서 만듬
- Layout : 웹 페이지 안에서 각 요소의 위치를 배치(<Reflow>)
- Painting : 웹 페이지 안의 요소들을 그리는 과정

### 업데이트 과정

예를 들어, 좋아요 버튼을 눌렀을 떄:

- Javascript로 DOM을 수정하면, Critical Rendering Path가 다시 실행됩니다.
- DOM 수정 후, Layout (<Reflow>) 과 Painting (Repaint) 과정이 다시 발생합니다.

### 중요 포인트

DOM을 최소화하는 것이 성능 저하를 방지하는 핵심입니다.
동시에 발생한 업데이트를 모아서 한 번에 수정하면 <Reflow>와 <Repaint>을 최소화할 수 있습니다.

### 2. React의 렌더링 프로세스

- Render Phase: 컴포넌트를 계산하고 업데이트 사항을 파악하는 단계
- Commit Phase: 변경사항을 실제 DOM에 반영하는 단계

#### 예제 코드

```javascript
function App() {
  return (
    <div id="main">
      <p>Hello</p>
    </div>
  );
}
```

#### React Element

컴포넌트 호출 시 생성되는 React Element 구조:

```javascript
{
    type: 'div',
    key: null,
    ref: null,
    props: {
        id: "main",
        children: {
            type: "p"
            ...
        }
    }
}
```

#### Virtual DOM 생성

- Virtual DOM : React Element들의 모임으로, 실제 DOM은 아니며, 값으로 표현된 UI라고 이해할 수 있습니다.

#### Critical Rendering Path

1. HTML, CSS 파싱
2. 렌더트리 생성
3. 레이아웃
4. 페인팅

#### 업데이트 발생 시

1. Render Phase를 처음부터 다시 실행하여 새로운 Virtual DOM을 생성
2. 업데이트가 반영된 Virtual DOM과 이전 Virtual DOM을 비교 (Diffing)
3. 실제 DOM에 업데이트 반영(Reconciliation)

#### Reconciliation

- 동시에 발생한 업데이트를 모아 한 번만 DOM을 수정하여 대부분의 상황에서 충분히 빠른 화면 업데이트를 제공합니다.

#### 결론

- 순수한 Javascript만으로 DOM을 조작할 떄는 DOM 수정을 최소화해야 합니다.
- Reflow와 Repaint을 최소화하기 위해 동시에 발생하는 업데이트를 모아 한 번에 처리해야 합니다.
