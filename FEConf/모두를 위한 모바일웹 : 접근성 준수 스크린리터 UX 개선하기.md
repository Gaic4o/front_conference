# 모두를 위한 모바일웹 : 접근성 준수 스크린리터 UX 개선하기

[모두를 위한 모바일웹 : 접근성 준수 스크린리터 UX 개선하기][https://www.youtube.com/watch?v=tKj3xsXy9KM]

김도환 - 토스, 클라이언트 플랫폼팀, 프론트엔드 개발자.

웹 접근성을 생각 해 보면?

- 장애를 가진 사람도 쉽게 쓸 수 있는.
- 키보드 인터랙션
- Semantic HTML
- WAI-ARIA 
- `스크린리더 (음성 메시지)` (여기에 초점 하여 발표)  


## 브랜드 캐시백의 접근성을 개선 해 봅시다.

- 배경 지식.

    - 스크린리더가 앱을 읽는 방법.
    - Accessible Name, Role, Children Presentational 
  
- 케이스 스터디.

    - Case 1 : 정체를 알 수 없는 버튼.
    - Case 2 : 이미지는 대체 텍스트가 필요하다.
    - Case 3 : 버튼일 수도 있고 아닐 수도 있다.
    - Case 4 : 선택/취소할 수 있는 카드.
    - Case 5 : 다가갈 수 없는 바텀샷.
    - Case 6 : 소리없는 토스트.

- 다시, 스크린리더로 `브랜드 캐시백` 써보기.



누구를 위한, 무엇에 대한.

- 대상 : 모든 프론트엔드 개발자.

- YES 
    `모바일 기기의 스크린리터`에 초점을 둔 접근성 사례.
    적은 노력으로 UX를 눈에 띄게 개선할 수 있는 사례.

- NO 
    스크린리더 사용법.
    스크린리더와 무관한 사례.

발표가 끝났을 떄 이런 상태가 됐으면 좋겠습니다.

- 내 서비스도 `스크린리더로 써봐야` 겠다.
- 조금만 신경써도 `UX를 많이 개선할 수 있구나,` 얼른 해 봐야지! 





배경 지식 : 스크린리더가 앱을 읽는 방법.

- 움직이는 단위: 요소 (HTML Element)
  
- 순차 탐색 : `순가락을 좌우로 스와이프`하면 앞뒤로 이동.
- 스크린 터치 : `터치한 영역`에 있는 요소를 선택.
- 로터를 이용한 탐색 : `머리말/단어/글자` 단위로 이동. 



먼저 FEConf 읽게 됩니다.
그 다음 토스 -> 프론트엔드 챕터 -> ... 
좌측으로 스와이프하면 다시 토스.

``` html
<div>FEConf</div>
<div>
 <div>
     <div>토스</div>
     <div>프론트엔드 챕터</div>
     <div>클라이언트 플랫폼 팀</div>
 </div>
 <div>김도환</div>
</div>
<div>2021</div>
```




배경 지식 : Accessible Name(이하 Name) 

동일 UI인데, `코드가 이렇게 다른 경우에는 스크린리더가 읽게 될까요?`

``` html
<div aria-label="A11Y는 중요합니다">
    접근성이 중요합니다.
</div>
```

- 스크린리더가 요소를 `포커스했을 떄 읽는 값.`

- 다음 중 하나로 결정.
    1. author : 특별 속성을 사용 해 정하는 값.
        - aria-label, aria-labeleedby, alt(<img>)
    
    2. contents : 요소의 테스트 값.

- `우선순위` : `author > contents`  (즉 author 우선 순위가 더 높기 떄문에 컨텐츠가 있다고 하더라도 이것을 사용 해서 읽게 됩니다.)




배경 지식 : Role 

- 스크린리더가 요소를 어떤 방식으로 다를 지 결정하는 방식.
- Role 마다 기대되는 스크린리더 동작이 있습니다.

Example role = "button" 
    - 요소의 `Accessible Name` 을 읽은 뒤 "버튼" 붙여 읽음.
    - 자식 요소의 Accessible Name 을 모아서 contents 로 사용.




배경 지식 : Semantic tag 와 Role 

- Semantic tag는 암시적으로 Role 을 갖고 있음.
    <button> : role = "button"
    <a> : role = "link" 
    <input type="checkbox"> role="checkbox"


``` html
<button>토스 지원하기.</button>
=== 
<button role="button">
    토스 지원하기.
</button>
```




배경 지식 : Children Presentational

``` html
<div>
    <span>span1</span>
    <span>span2</span>
    <span>span3</span>
</div>
```

1. 버튼 / span1 / span2 / span3 
2. span1 span2 span3,  버튼 

- 특정 Role 이 가진 특징.
- `자식 요소의 Accessible Name 을 모아 요소의 content 를 사용.`
- 자식 요소를 스크린리더가 읽지 않도록 합니다.
- `시각 사용자가 묶어 이해하는 정보를 스크린리더가 끊어읽는 문제를 해결할 떄 유용합니다.`




케이스 스터디.


Case 1: 정체를 알 수 없는 버튼.

P 아이콘이 있습니다.
시각 사용자는 P 아이콘을 보고 어떤 생각?

- 포인트 페이지로 가는 버튼이네.

스크린리더는 우측 상단 버튼을 버튼이라 생각합니다.
무슨 기능을 하는 버튼인지 알 수 가 없습니다.

``` javascript
<button onClick="moveToPointPage()">
    <img src="/point-logo.png" alt="">
</button>
```

배경 지식 : <img> 와 alt 

`alt 값이 "" 빈 값이라면?` 스크린리더가 읽지 않습니다.
반드시 alt 를 명시해야 합니다.


배경 지식 : Children Presentational

- 자식 요소의 Accesible Name 을 모아서 요소의 contents 로 사용.

Button 의 컨텐츠는 자식들의 합이라고 볼 수 있습니다.
그러면 이 버튼 컨텐츠는 곧 이미지 태그의 Accessible Name 의 "내 포인트 내역으로 이동"이 됩니다.

곧 버튼 Accessible Name 이 되서 "내 포인트 내역으로 이동" 이 되는 것 입니다.
그래서 이 버튼을 스크린리더가 포커스 했을떄 -> `내 포인트 내역으로 이동, 버튼` 이라고 읽게 되는 것 입니다. 






Case 2 : 대체 텍스트가 없는 이미지.

(문서 이미지에 큰 의미를 부여하지 않음) 
스크린리더는 "imgempty.png, 이미지" 이렇게 읽고 있습니다.

장식용 이미지를 알 수 없는 단어로 읽고 있습니다.

alt 명시하지 않으면 반드시 alt 명시 해주어야 합니다.
alt 에 빈 값을 주면 스크린리더가 읽지 않습니다.

``` html
<img 
    src="/imgempty.png"
    alt="" 
>
```




Case 3 : 버튼일 수도 있고 아닐 수도 있습니다.

저 브랜드 20% 캐시백에 한도가 1천원이네.
`화살표를 보니 상세 페이지로 가는 버튼인가보다.`


문제점 : 

- 1 : 브랜드"명 / 20 / 퍼센트 ... 가 버튼이라는 걸 모름.
 `해결 : role="button"`
- 2 : 하나의 정보를 끊어 읽어서 이해가 어렵습니다.
 해결 : Children Presentational 

이 문제점을 어떻게 해결 할 수 있을가요?

- `button 이라는 role 을 사용하면 될 것 같습니다.`
- children 배경지식 사용하기. 이 요소의 자식 Accessible Name 을 모아 정보를 모아 읽을 수 있을 것 같습니다.




- 3. 선택된 상태인지 모름.

배경 지식 : `role = "checkbox"`

- 스크린리더가 "체크박스" 라고 읽음.
- aria-checked="true|false" 와 함께 사용하면, "선택됨/선택 해제됨"도 읽어줌.
- Children Presentational 특징을 가지고 있는 role 입니다.

``` html
<!-- 선택 안 된 카드 -->
<div
    role="checkbox" aria-checkbox="false" 
>
    <img src="/myongrang-hotdog-logo.png" alt="">
    <span>명량핫도그</span>
    <span>30</span>
    <span>%</span>
</div>
```







Case 5 : 다가갈 수 없는 바텀시트.

바텀시트라고 하는 것은 스크린샷에 잇는 하단 빨간색 표시 . 바텀시트.
스크린샷을 봤을 댸 어떤 경험을 느낄까요?

- 카드를 눌렀더니 어디서 결제했는 지 물어보는 구나.
그런데 스크린리더는 이 경우에 어떤 식으로 처리를 하고 있을까요?


문제점.

- 바텀시트가 열렸지만 스크린리더 사용자는 알 수 없습니다.

배경 지식 : role="dialog"

- dialog: 사용자가 상호작용 할 수 있는 대화상자.
  
- aria-modal : 스크린리더가 dialog 밖의 요소에 포커스 할 수 없게 만드는 속성.
    - 스크린리더가 dialog 만 포커스하게 되므로 사용자가 dialog 를 잘 인지 할 수 잇음.
    - 스크린리더가 구현하지 않은 경우가 있어 직접 구현이 필요합니다.
    - TODO : 모달인 dialog 가 열렸을 댸, dialog 밖을 포커스 할 수 없도록 만들기.



배경 지식 : aria-hidden 

- 요소에 aria-hidden="true" 를 명시할 경우, 스크린리더가 해당 요소와 자식을 읽지 않습니다.


배경 지식 : aria-hidden 

``` html
<div aria-hidden="true">
    <span>span1</span>
    <span>span2</span>
    <span>span3</span>
</div>
```

- 스크린리더는 이 요소를 읽지 않고 넘어가게 됩니다. 왜? aria-hidden 이 true 이기 떄문입니다.


기존 코드는  이ㅎㅔ 되 있ㅡㅂ니다.

``` javascript
<body>
    <div>
        <h1>어떤 카드로 결제 하셨어요?</h1>
        <h2>토스에 등록된 카드를 보여드려요 결제하셨어요?</h2>
    </div>
    <div>
        <button>토스 카드</button> // 프론트엔드 카드, 버튼 
        <button>프론트엔드 카드</button> 
        // ....
    </div>
    // 바텀시트.
    <div>
        <div>
            <header>어디에서 결제했나요?</header>
            // ...
        </div>
    </div>
</body>
```

바텀시트 태그에 다이얼로그를 추가하고
스크린리더용 버튼을 추가 해 줍시다.

그리고 스크립트 작성.
버튼을 눌렀을 떄 모달을 생기게 하는 버튼.

모든 요소에 aria-hidden 을 true 로 바꾸는 코드.

``` javascript
function openModal(dialogContainerElement) {
    [...document.body.children].forEach(element => {
        if (dialogContainerElement !== element) {
            element.setAttribute('aria-hidden', 'true'); 
        }
    });
}
```

카드를 누르면 -> aria-hidden 이 true 로 설정되면서 dialog 가 나오고.
-> 닫기 버튼이라는 스크린리더가 설명 해 줍니다.

``` html
<body>
    <div aria-hidden="true">
        <h1>어떤 카드로 결제 하셨어요?</h1>
        <h2>토스에 등록된 카드를 보여드려요 결제하셨어요?</h2>
    </div>
    <div aria-hidden="true">
        <button>토스 카드</button> // 프론트엔드 카드, 버튼 
        <button>프론트엔드 카드</button> 
    </div>
    <div role="dialog">
        <div>
            <button className="sr-only">닫기</button>
            <header>어디에서 결제했나요?</header>
        </div>
    </div>
</body>
```


토스카드, 버튼
(클릭)
닫기, 버튼
어디에서 결제했나요? 








Case 6 : 소리없는 토스트

스크린샷을 보면 최대 2개까지 선택 할 수 있습니다.
`토스트를 보면서 생각 할 수 있습니다.`

-> 2개까지만 추가할 수 있구나.

스크린리더는 어떻게 읽고 있을까요?
-> `토스트가 떴지만, 왜 아무소리도 안 내는 것이지?`


문제점 : 토스트를 띄워서 더 선택할 수 없다는 경고를 했지만, 눈으로만 확인할 수 있습니다.


배경 지식 : role = "alert" 

- 다음과 같은 상황에서 스크린리더가 해당 요소를 읽어줍니다.
    요소가 DOM Tree 에 추가되었거나,
    요소의 자식에 변경사항이 생겼을 떄.



Before 

``` html
<!-- 토스트 -->
<div>
    최대 2개 추가할 수 있어요
</div>
```
-> 아무런 반응이 없습니다.

`alert 이라는 role 를 추가 해 주면 소리가 들립니다.`

``` html
<div role="alert">
    최대 2개 추가할 수 있어요 
</div>
```



직접 해 볼 수 있어요.

- 일단 스크린리더로 서비스 써보기.
- role, aria-* 써 보면서 이것저것 실험해보기.
    codepen.io 같은 웹 에디터 활용하기.
    w3, w3c 스펙 읽어보기.
- WAI-ARIA authoring practices / react-aria 