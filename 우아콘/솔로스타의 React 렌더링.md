# [솔로스타의 React 렌더링](https://www.youtube.com/watch?v=eBDj0B0HbEQ)

## 렌더링이란?

코드를 실행하면 웹 페이지가 그려짐.
여기서 객체 버츄얼 돔이 중간에 있음.

함수 ->(render 가정) 객체(VirtualDom) ->(commit) Dom(RealDOM)
개발자는 함수 컴포넌트만 만들면 나머지는 React가 알아서 해 줌.

간혹 컴포넌트에서 realDOM을 사용할 때는 Ref을 사용하기도 함.
리액트 컴포넌트는 렌더링 할 때 마다 Fiber Node라는 저장 공간이 있음.

렌더링 된 결과는 객체임(VirtualDOM) -> Real DOM에 반영되면 웹 페이지가 됨

## 리-렌더링

컴포넌트가 있음.
리액트는 useState을 만나게 되면 초기값을 Fiber Node에 저장함.
나머지는 동일하게 렌더링이 완료 됨.

setState을 하면 상태를 변경시키고, 리렌더링을 발생시킴.
Fiber Node에서 다시 가져와서 활성화 시킴.

## 리-렌더링 최적화

리액트 입장에서는 리-렌더링이 발생할 떄 모든 컴포넌트가 리렌더링이 발생하니 리-렌더링아 발생할 부분만 React.memo을 적용시킬 수 있음.
리-렌더링을 할 떄 동일 count 값을 넘겨주면 그대로 사용하게 됨. count을 다르게 주면 리렌더링을 하게 됨.

who, handleClick 객체와 함수를 props 으로 넘겨주기. who 라는 함수가 새로 만들어 지기 떄문에, 참조가 다르다고 판단함.
handleClick도 마찬가지로 Profile() 함수가 호출 될 떄마다 다르다고 생각하고 리-렌더링이 됨. 값을 바꾸지 않아도.

useMemo, useCallback을 사용하면 이 값을 Fiber Node에 저장하게 됨.
두 개의 값을 동일 값을 가져와서 동일하다고 생각함. handleClick도 마찬가지임.

## 재조정

이전 렌더링 결과, 현재 렌더링 결과를 비교해 변경된 부분만 반영하는 작업

Case 1: 리-렌더링 후 엘리먼트가 달라졌을 떄
Case 2: 리-렌더링 후 동일한 엘리먼트들의 순서가 달라졌을 때

https://www.youtube.com/watch?v=eBDj0B0HbEQ
