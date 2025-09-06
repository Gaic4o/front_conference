# [룩소의 React Hooks](https://www.youtube.com/watch?v=qjEcsNYFWYg)

# React Hooks

Class 컴포넌트에서 this 문제점이 있음.
this 는 컴포넌트의 인스턴스를 가리킴. 그런데 this 안에는 props, state 등 변경 가능한 값들이 들어있음.
문제는 여기서 비동기 작업(setTimeout, event, API 응답 등) 안에서 this를 사용하면, 코드가 실행될 댸 시점이 아닌
최신 값으로 바뀐 this을 참조 가능.
즉 정리해보자면 -> Class 컴포넌트는 this가 계속 변하므로, 비동기 코드에서 내가 원하던 값이 아닌 최신 값이 사용되는 버그가 발생한다.
Hook는 함수 클로저로 그 시점의 값을 안전히 기억할 수 있어, 이런 문제를 깔끔히 해결

```ts
function FollowButton({ user }) {
  return (
    <button
      onClick={() => {
        const currentUser = user;
        setTimeout(() => {
          alert(currnetUser + "님이 팔로우했습니다.");
        }, 3000);
      }}
    >
      팔로우
    </button>
  );
}
```

Class 컴포넌트에서 상태 로직 재사용 힘듬, HOC 패턴 같은 방법으로 해결
함수형은 상태 관리, 생명 주기 기능 여러 가지 장점이 있음.
Hooks은 더 깔끔한 코드 즉 가독성 있는 코드를 작성할 수 있음.

Mount -> 처음 컴포넌트가 생성되고, 렌더링 거치는 과정 `componentDidMount()`
Update -> props, setState, 부모 컴포넌트에서 -> 리렌더링 됐을 때 `componentDidUpdate()`
Unmount -> 제거 될 떄 컴포넌트가 종료되는 과정 -> `componentWillUnmount()`

발표자는 Hooks로 사용자 인증 로직을 컴포넌트 Wrapper 형식으로 만들어서 깔끔하게 적용.
Wrapper 지옥에 빠지게 될 수 있음 하지만 Custom Hook 을 이용해서 문제를 해결 할 수 있음.
