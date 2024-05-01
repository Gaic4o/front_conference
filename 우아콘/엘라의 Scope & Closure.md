[🍧 엘라의 Scope & Closure](https://www.youtube.com/watch?v=PVYjfrgZhtU)

# 스코프와 클로저

## 스코프(Scope)

스코프는 함수, 변수, 클래스와 같은 식별자의 가시성과 생명주기를 결정합니다.
식별자가 선언된 위치에 따라 다른 코드에서 참조될 수 있는 지 여부가 결정됩니다.

```javascript
function add(x, y) {
  console.log(x, y); // 3, 6
  return x + y;
}
add(3, 6);
console.log(x, y); // ReferenceError: x is not defined
```

### 스코프 종류

1. 전역 스코프(Global Scope): 전역에 선언된 변수는 어디에서나 참조 가능합니다.
2. 지역 스코프(Local Scope): 함수나 블록 내부에서 선언된 변수는 해당 지역 내에서만 참조가 가능합니다.
   - 블록 레벨 스코프 (if, for, while 등의 블록 내에서 선언된 변수는 블록 내에서만 유효)
   - 함수 레벨 스코프 (오직 함수 내에서만 유효 스코프, var 키워드로 선언된 변수가 이에 해당)
   - `let`, `const` ES6부터 블록 레벨 스코프를 제공하며, Javascript 에서도 블록 레벨 스코프를 사용할 수 있게 되었습니다.

### 스코프 결정 시기

1. 동적 스코프 : 함수가 호출될 떄 스코프가 결정됩니다.
2. 정적 스코프 : 함수가 정의될 떄 스코프가 결정됩니다. Javascript는 이 방식을 따릅니다.

### 스코프 체인

함수 내부에서 다른 함수가 중첩될 수 있습니다.
외부 함수의 스코프에서 내부 함수의 스코프로 이어지는 링크가 형성됩니다.
이러한 연결을 스코프 체인이라고 하며, 하위 스코프에서는 상위 스코프의 변수를 참조할 수 있습니다.

```javascript
var x = "나는 전역 x야";

function outer() {
  // outer 함수의 스코프
  var y = "나는 outer함수의 지역 y야";
  console.log(x); // 1: 나는 전역 x야
  console.log(y); // 2: 나는 outer 함수의 지역 y야
  function inner() {
    // inner 함수의 스코프
    var x = "나는 inner함수의 지역 x야";
    console.log(x); // 3: 나는 inner 함수의 지역 x야
    console.log(y); // 4: 나는 outer 함수의 지역 y야
  }
  inner();
}
outer();
console.log(x); // 5: 나는 전역 x 야
console.log(y); // 6: ReferenceError 어쩌고
```

## 클로저(Closure)

클로저는 중첩된 내부 함수가 외부 함수의 변수를 참조할 수 있게 하는 기능입니다.
이는 외부 함수와 실행 컨텍스트가 종료된 후에도 외부 함수의 변수에 접근할 수 있습니다.

```javascript
const x = 1;

function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  };
  return inner;
}
const ella = outer();
ella(); // 10
```

### 클로저 설명

- `outer` 함수가 종료된 후에도 `inner` 함수는 `outer` 함수의 변수 `x`에 접근할 수 있습니다.
- 이 현상은 `outer` 함수의 렉시컬 환경이 `inner` 함수에 의해 참조되기 떄문에 발생합니다.
- 클로저는 함수와 함수가 선언된 렉시컬 환경의 조합입니다.

#### 렉시컬 환경

- 자바스크립트에서 스코프와 식별자가 관리되는 방식을 설명하는 중요한 개념입니다.

렉시컬 환경은 두 가지 요소를 가집니다.

1. 환경 레코드 : 선언된 식별자(변수, 함수, 클래스 등)와 그에 바인딩된 값을 저장하는 구조
2. 외부 렉시컬 환경에 대한 참조 : 현재 렉시컬 환경이 생성된 위치의 외부 코드에 대한 참조를 포함합니다.
