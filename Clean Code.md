# Coss Conference 

[Clean Code][https://toss.im/slash-21/sessions/3-3]

<h2>Clean code 정리 내용</h2>

클린 코드? -> 명확한 이름, 중복 줄이기를 먼저 말하곤 합니다.
_실무에선 이 외에도 조금 더 섬세하게 코드를 정리하는 스킬이 필요합니다._

## 발표 : 진유림 Toss Frontend Developer 

1. 실무에서 클린 코드의 의의.

흐름 파악 어렵고, 도메인 맥락 표현이 안 되어 있음, 동료에게 물어봐야 알 수 있는 코드 -> 개발할 떄 병목 현상, 유지보수 힘듬, 심하면 기능 추가가 불가능 합니다.

실무에서 클린 코드의 의의 = 유지보수 시간의 단축.
-> 내가 짠 코드를 다른 동료들이 빠르게 이해 할 수 있다면 유지보수 할 떄 드는 개발 시간이 짧아 집니다. 

읽기 좋은 깔끔 코드는 -> 코드리뷰 시간도, 버그 디버깅 시간도 단축시킵니다.
시간은 자원이며, 곧 돈입니다.



### 안일한 코드 추가의 함정.

기존 코드를 보면,

``` javascript
    function QuestionPage() {
        // 연결 중인 전문가가 있으면 팝업 띄우는 로직 추가. 
        async function handleQuestionSubmit() { 
            const 약관동의 = await 약간동의_받아오기();
            if (!약관동의) {
                await 약관동의_팝업열기();
            }
            // 질문 전송 성공 alert 
            await 질문전송(questionValue);
            alert('질문이 등록되었어요.');
        }
        return (
            <main>
                <form>
                    <textarea placeholder="어떤 내용이 궁금한가요? "/>
                    // 팝업 컴포넌트 추가 
                    <Button onClick={handleQuestinSubmit}>질문하기></Button> 
                </form>
            </main>
        )
    }
```

만약 이 코드에 새로운 기능을 추가한다고 하자 어떻게 추가 하면 될까요?
기존에 있는 Click 함수에서 연결 중인 전문가가 있는 지 확인하는 코드를 넣고, 있으면 팝업을 띄우면 됩니다. 

버튼 컴포넌트 아래에 넣고 처음엔 숨겨두고 필요할 떄 보여 줄 것입니다.


``` javascript

function QuestionPage() {
    const [popupOpened, setPopupOpened] = useState(false); // 팝업 상태. 

    async function handleQuestionSubmit() {
        const 연결전문가 = await 연결전문가_받아오기(); // 연결 중인 전문가가 있으면 팝업 띄우기. 
        if (연결전문가 !== null) {
            setPopupOpened(true);
        } else {
            const 약관동의 = await 약관동의_받아오기();
            if (!약관동의) {
                await 약관동의_팝업열기();
            }
            await 질문전송(questionValue);
            alert('질문이 들어왔어요'); 
        }
    }
}

async function handleMyExpertQuestionSubmit() { // 팝업 버튼 클릭 핸들러. 
    await 연결전문가_질문전송(questionValue, 연결전문가.id);
    alert(`${연결전문가.name}에게 질문이 등록되었어요.`); 
}
return (
    <main>
        <form>
            <textarea placeholder="어떤 내용이 궁금한가요?" />
            <button onClick={handleQuestionSubmit}>질문하기</button>
        </form>
        {popupOpened && ( // 팝업 컴포넌트. 
            <연결전문가팝업 onSubmit={handleMyExpertQuestionSubmit} />
        )}
    </main>
)
```

이것은 타당하고 좋은 코드로 보이지만 
_나쁜 코드 입니다!_

하나의 목적인 코드가 흩뿌려져 있습니다.
지금 업데이트 한 코드는 모두 연결 중인 전문가 팝업 관련 코드 입니다.

이들이 뚝뚝 떨어져 있어서 나중에 기능을 추가할 떄 스크롤을 위아래로 이동하면서 미로찾기를 해야 합니다. 

즉 지금 코드를 보면 _기존에 있던 함수가 이제 총 3가지의 일을 추가 해 하고 있습니다._

세부 구현을 모두 읽어야 함수의 역할을 알 수 있게 됩니다.
코드 추가 및 삭제도 시간이 오래 걸리게 됩니다.


_마지막으로 함수의 세부구현 단계가 제각각이라는 문제가 있습니다._

그리고 함수명도 handleQuestionSubmit(), handleMyExpertQuestionSubmit() 비슷합니다.
깔끔했던 코드가 작은 기능 하나를 추가했더니 어지러운 코드가 되었습니다.
 
이제 큰 크림을 보며 코드 리팩토링을 해 봅 시다.

일단 함수 세부 구현 단계를 통일 했습니다.


``` javascript
function QuestionPage() {
    const 연결전문가 = useFetch(연결저문가_받아오기); 

    async function handleNewExpertQuestionSubmit() {
        await 질문전송(questionValue);
        alert('질문이 등록되었어요.')
    }

    async function handleMyExportQuestionSubmit() {
        await 연결전문가_질문전송(questionValue, 연결전문가.id);
        alert(`${연결전문가.name}에게 질문이 등록되었어요.`);
    }
}
```

기존에 else if 문으로 하나의 함수로 작성했었는데, 이것을 함수 2개로 분리하고 각각의 기능들을 추가 해 줬습니다.
확실하게 else if 문으로 봤던 코드보다 훨씬 깔끔하고 이해하기 쉽게 되었습니다.


그리고 하나의 목적인 코드를 뭉쳐 두었습니다.

``` javascript
return (
    <main>
        <form>
            <textarea placeholder="어떤 내용이 궁금한가요?" />
            {연결전문가.connected ? (
                <PopupTriggerButton
                    popup={(
                        <연결전문가팝업
                        onButtonSubmit={handleMyExpertQuestionSubmit} />
                    )}
                >
                질문하기 </PopupTriggerButton>
            ) : ( 
            )}
        </form>
    </main>
)
```

기존에는 팝업을 여는 버튼과 팝업 코드가 동떨어져 있었습니다.
이를 모아서 PopupTriggerButton 이라는 컴포넌트를 만들어 주었습니다.

함수가 한 가지 일만 하도록 쪼개기.

``` javascript
<Button onClick={async () => {
    await openPopupToNotAgreedUsers();
    await handleMyExpertQuestionSubmit(); 
    }}
    >
    질문하기
    </Button>
    )}
    </form>
    </main>
    )
    }
    async function openPopupToNotAgreedUsers() {
        const 약관동의 = await 약관동의 받아오기();
        if (!약관동의) {
            await 약관동의_팝업열기(); 
        }
    }
```

약관동의 관렴 함수를 쪼개 필요한 시점에 보도록 만들었습니다.
하지만 코드가 길어졌습니다.

클린 코드 != 짧은 코드 == 원하는 로직을 빠르게 찾을 수 있는 코드.


원하는 로직을 빠르게 찾으려면?


하나의 목적을 가진 코드가 흩뿌려져 있습니다 -> 응집도.
함수가 여러 가지 일을 하고 있습니다. -> 단일책임.
함수의 세부 구현 단계가 제각각입니다. -> 추상화. 




### 로직을 빠르게 찾을 수 있는 코드.

1. 응집도 : 같은 목적의 코드는 뭉쳐 두자.








2. 액션 아이템.


