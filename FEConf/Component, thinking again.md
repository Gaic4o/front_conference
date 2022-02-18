# Component, thinking again

[Component, thinking again][https://www.youtube.com/watch?v=HYgKBvLr49c&t=1255s]


케이크는 -> `밀가루`, `설탕`, `계란`에 의존성을 갖는다고 할 수 있습니다.
의존성이란 단어를 많이 사용.

React 컴포넌트에서는 의존성이 어떤 게 있을까요?
React 컴포넌트를 만들려면 무엇이 필요? -> Props, Hooks 받을 수 있습니다.`(기능적 분류)` 

Props, Hooks 로 넘기거나, Import 가져오는 의존성에는 어떤 특징? 

스타일 - `컴포넌트 CSS 스타일` import (필요한 의존성).
로직 - `UI 조작하는 특정 동작, 컴포넌트에 의도한 사이트 이펙트를 주거나 할 떄 사용 (커스텀 훅).`
전역 상태 - 유저 액션을 통해 초래된 클라이언트 (상태 의미. 로그인 정보, 전체메뉴 열고닫기 URI 표현)
리모트 데이터 스키마 - 원격, 즉 api 서버에서 내려주는 데이터 의미. 스키마(담는 그릇) (json 데이터 -> 내용, 모양)

더 깊이 살펴봐야 하는 의존성이 있습니다.
`리모트 데이터 스키마 입니다.`

1. 로딩 드르륵.
2. 모든 컴포넌트가 준비 됐을 떄 드르륵. 

보통 2번쨰 동작을 원합니다.


케이크에 딸기를 새로 얹어본다고 하면? 

케이크에 `딸기`를 추가하려면?
`딸기`뿐 아니라 `생크림`도 필요합니다.

-> 딸기 케이크는 생크림에 추가적으로 의존합니다.
딸기 케이크의 숨은 의존성 : `생크림`



React 컴포넌트에 새로운 정보를 추가 해본다고 하면?

새로운 정보를 추가 하기 위해서 새로운 Props 정의.
`다른 수정도 필요하게 됩니다.`
-> 정보를 표현하는 React 컴포넌트의 숨은 의존성 : ?

<Article /> 컴포넌트에 정보 새로 추가하기.
 
리모트 데이터 <Article /> 컴포넌트의 Props 수정.
-> 
React 컴포넌트 <Article /> 렌더링 부분 수정.
-> 
숨은 의존성 <ArticleList />의 Props 수정.
->
숨은 의존성 <PageArticleList /> useEffect 훅 수정.

루트 컴포넌트 와 현재 컴포넌트 사이의 수 많은 컴포넌트를 
따라서 이는 `리모트 데이터 스키마가 가진 숨은 의존성이 됩니다.`

만약 Props Drilling 을 피하기 위해서, `데이터 저장소를 따로 두더라도,`
페이지 기반 라우팅을 한다면? 결국 루트 컴포넌트에 의존할 수 밖에 없을 것 입니다.




## 함께 두기 Co-locate 

첫 번째 리팩토링 원칙 -> 비슷한 관심사라면 가까운 곳에 두기.
`파일들이 서로 멀어지게 되면 집중력이 분산 되게 쉽습니다.` (같은 파일, 바로 옆에 두기) 

가장 쉽게 함께 둘 수 있는 게 어떤 것이 있을까요?
먼저 `전역 상태는 여러 컴포넌트가 함께 사용하는 것이니`, 아무래도 함께 두기 힘듬.

`스타일`과 `로직`은 함께 두기가 쉽게 가능 할 것 같습니다.
컴포넌트 안에 내재하도록 바꿔보기 (스타일 CSS-in-JS) (로직 Custom Hooks)


``` javascript
const Something: React.FC = () => {
    const { ... } = useHooksForSomething()
    return <Container>Hello, World</Container>
}

// 로직
function useHooksForSomething() {
}

// 스타일.
const Container = styled.div`
    background-color: red;
`
```

로직과 스타일을 근처에 두기.
로직, 스타일을 변경 할 떄 -> `여러 파일을 오가지 않아도 함꼐 수정할 수 있게 됩니다.`
--> 코드가 길어진다면? 다른 폴더가 아닌 `같은 폴더 내에 다른 파일로 분리하는 것을 추천.`

이번엔 리모트 데이터 스키마를 같이 둘려면?

리모트 데이터 스키마 -> 루트 컴포넌트 -> 다른 컴포넌트 -> (props) 내 컴포넌트.
props 전달 받으면? 루트 컴포넌트와 강한 의존성이 생깁니다.

Props ID 를 받고, 데이터 저장소에서 ID를 통해.
해당 데이터를 받아 올 수 있게 해서 의존성을 끊어 낼 수 있습니다.

``` javascript
import { IArticle } from '~/api'

interface Props {
    article: IArticle 
}

const Something: React.ExoticComponent<Props> = (props) => {
    return (
        <div>
            <h1>{props.article.title}</h1>
        </div>
    )
}
```

-> `이 컴포넌트를 개선 해 본다면?`
이렇게 하면 확실히 다른 컴포넌트와의 의존성이 많이 약해졌다는 것을 확인.

``` javascript
interface Props {
    articleId: string 
}
const Something: React.ExoticComponent<Props> = (props) => {
    const article = useArticle(props.articleId) 

    return (
        <div>
            <h1>{article.title}</h1>
        </div>
    )
}
```



두 번째 리팩토링 원칙 -> `데이터를 ID 기반`으로 정리하는 것.

``` javascript
{
    id: '123',
    author: {
        id: '1',
        name: 'Paul',
    },
    title: 'My awesome blog post',
    comments: [
        {
            id: '324',
            commenter: {
                id: '2',
                name: 'Nicole', 
            },
        },
    ],
}
```


API 응답을 다음과 같이 정리한다면?
`모델명`과 `ID` 를 가지고 `특정 데이터`를 뽑아 낼 수 있습니다. -> 데이터 정규화, Normalization 

``` javascript
{
    result: '123',
    entities: {
        articles: {
            '123': {
                id: '123',
                author: '1',
                title: 'My awesome blog post',
                comments: ['324'],
            },
        },
        users: {
            '1': { id: '1', name: 'Paul' },
            '2': { id: '2', name: 'Nicole' },
        },
        comments: {
            '324': { id: '324', commenter: '2' },
        }
    }
}
```

이렇게 `데이터 정규화를 도와주는 라이브러리`가 있습니다.

``` md
$ yarn add normalizr 
```

이런 정규화된 데이터를 가지고 특정 객체를 `데이터 저장소로`부터 쉽게 가져 올 수 있습니다.

상위 컴포넌트 -> 초콜릿: 3 -> 컴포넌트 -> 3번 초콜릿을 주세요 -> 데이터 저장소.

바로 Global ID, 한국말로 전역 아이디입니다. 
전역 아이디는 특정 객체를 식별하기 위해, 모델명을 따로 넘길 필요 x 아이디값으로 특정 데이터를 유일하게 식별할 수 있는 체계.

보통 모델명과 ID 값을 string concat 해서 생성 하고, 경우에 따라서 base64 인코드, 디코드도 할 수 있습니다.
useArticle Hook -> `Article 필요하다 역시 컴포넌트 바깥에서 주입받고 있는 것을 확인 할 수 있습니다. (import) `

전역 ID를 통해 데이터를 가져오는 useNode hooks 로 바꾸면?
사용할 모델 정보마저, 컴포넌트 내부에 함께 둘 수 있게 됩니다.


``` javascript
import { useNode } from '~/store' 

interface Props {
    articleId: string 
}
const Something: React.ExoticComponent<Props> = (props) => {
    const article = useNode({ on: 'Article' }, props.articleId)

    return (
        <div>
            <h1>{article.title}</h1>
        </div>
    )
}

// Global ID 를 활용해,
// 사용할 데이터의 모델 마저 컴포넌트 내부의 Co-locate (의존성 줄이기) 
```


한발 짝 더 나아가서 

GOI(Global Object Identification) 에 대해 알아봅시다.
이런 API 서버 측에 제공하면? 해당 객체를 불러올 수 있게 됩니다.

클라이언트 -> GET /node/C#3 -> API 서버.

어떠한 코드 위치, 맥락에 있든
Global ID 기반으로 특정 객체를 가져올 수 있는 API 



컴포넌트 
-> C#3 의 A,B 필드를 주세요.
데이터 저장소. (데이터 저장소에 특정 필드가 없음) 
-> GET /node/C#3
API 서버.


다음 형태로 어떤 필드를 사용 할 지 표현 할 수 있습니다.

``` javascript
const article = useNode(
    {
        on: 'Article',
        fields: {
            title: true, 
            content: true,
        },
    },
    props.articleId
)
```

루트 컴포넌트 가져온 데이터를 -> 정규화 해 -> 데이터 저장소 넣기.
우리가 작성 컴포넌트 -> 상위 컴포넌트로 -> 글로벌 아이디를 받고 이것으로 데이터 저장소에 특정 데이터를 전송합니다.

여기서 만약 ?
우리가 원하는 필드가 없다고 가정. 
데이터 저장소는 GOI를 활용 해 데이터 요청. 
그리고 데이터를 언제나 보장 받을 수 있습니다. (서버) 


특정 컴포넌트를 새로고침 해야 한다면?
UI에서 새로고침 할 수 있는 버튼 노출, 수정 일어났을 떄 refetch 호출하는 경우가 많은 데,
useNode 에 절차적으로 호출 할 수 있는 refetch 호출하면 상위 컴포넌트 관련 없이 refetch 로직을 쉽게 구현 가능 합니다.

GOI 활용 사례 #2 - Refetch 

``` javascript
const { article, refetch } = useNode(
    {
        on: 'Article',
        fields: {
            title: true,
            content: true, 
        },
    },
    props.articleId
)

const onRefreshButtonClick = () => {
    refetch() // refetch 쉽게 구현 가능. 
}
```




## 이름 짓기 Naming 

이 챕터에서는 `Props Nameing` 이야기.


간단 유저 Profile 보여주는 컴포넌트.
친구목록, 게시글에서도 사용 할 수 있을 것 같습니다.

의존성 살펴보기.

on User 

name,
nickname,
totalIFollowerCount
lastActivityAt
image -> on Image (thumbnailUrl)

그러면 Props nameing 해보기.

``` javascript
interface Props {
    leftImageThumbnailUrl: string
    title: string
    title2: string
    caption: string
    rightDotColor: string
    rightCaption: string 
}
```


의존한다면? 그대로 드러내기.

의존성을 그대로 드러내도록 수정.
user, ImageModel 사용하는 것을 그대로 드러냈습니다.

하지만, User와 ImageModel 관계 모델은 하지 않앗습니다.

``` javascript
interface Props {
    // 한 컴포넌트에서 여러 모델을 사용하는 것은 관심사를 제대로 하지 못했다는 점을 뜻이기도 합니다. 
    user: {
        name: string
        nickname: string
        totalFollowerCount: number
        lastActiivityAt: Date 
        image: {
            thumbnailUrl: string 
        }
    }
}
```



그러면 user모델과 image 모델로 분리 할 수 있게 됩니다.

``` javascript
interface Props {
    // 한 컴포넌트에서 여러 모델을 사용하는 것은 관심사를 제대로 하지 못했다는 점을 뜻이기도 합니다. 
    user: {
        name: string
        nickname: string
        totalFollowerCount: number
        lastActiivityAt: Date 
        image: {
            thumbnailUrl: string 
        }
    }
}

// 하지만 상위 컴포넌트가 해당 모양을 정확하게 맞춰줘야 하므로 상위 컴포넌트와 의존성이 생길 것 같습니다 
// 재사용 하기 힘들것 같습니다.
interface Props {
    image: {
        thumbnailUrl: string 
    }
}
```


전역 ID 를 통해 레퍼런스만 받아오기.

``` javascript
interface UserCardProps {
    userId: string 
}

const user = useNode({
    on: 'User', 
    fields: {
        name: true,
        nickname: true,
        totalFollowerCount: true,
        lastActivityAt: true,
        image: true, 
    },
}, props.userId)


interface AvatarProps {
    imageId: string 
}

const image = useNode({
    on: 'Image',
    fields: {
        thumbnailUrl: true, 
    }
}, props.imageId)
```






## 재사용 하기 

컴포넌트를 재사용하는 이유
-> 개발 할 떄 편리하기 위한 것보다 `변경 할 떄 편리하기 위해.`

변경 될 만한 부분을 예측하고 준비 해야 합니다.

제품의 성격에 따라 다르겠지만 대부분 변화는 리모트 데이터 스키마(변화의 방향성) 에 따라 변화합니다.
어떻게 대응 할 수 있을까요?

유저 표현 컴포넌트 vs (페이스북)페이지와 같은 당근마켓 페이지를 표현하는 컴포넌트.

리모트 데이터 스키마 -> User Page 로 각각 다릅니다. 
이 둘은 얼픽 보면 같겠지만 서로 다릅니다.

이런 상황에서 기존 컴포넌트에서 재사용? 복사 해 새 컴포넌트로 분리할까?


기존 컴포넌트를 재 사용하는 경우.
-> 페이지 특별 (페이지의 프로필 사진을 둥근 사각형으로 바꾸자)

만약 유저와 페이지 같이 쓴다면? 대응하기 힘듬.
해당 코드가 관여하고 잇는 부분이 넓게 퍼져 있어 버그 발생 오류 가능.

함께 변화해야 하는 것들과 따로 변해야 하는 것들을 어떻게 구분 할 수 있을까요? -> `모델 기준으로 컴포넌트 분리하기.`

각자 컴포넌트는 각자의 방향대로.
유저에게 일관된 경험을 줘야 함.

대 부분 모델을 기반으로 하므로, 변화의 방향성 역시 모델 명으로 정렬.
컴포넌트 수정은 매우 쉽게 할 수 있을 거 같습니다.

이런 고민이 든다면?

`같은 모델`을 의존하는 컴포넌트 : `재사용`.
`다른 모델`을 의존하는 갖는 컴포넌트 : `분리`.

당골 소식이라는 카드와 다른 카드와 비슷하지만 더보기 버튼이 있거나, 댓글 갯수는 보여주는 등 다른 카드와 다른 상황을 볼 수 있습니다.
4번 쨰 원칙에 의해 미래에 변화한다는 사실은 해당 컴포넌트 리모트 데이터 스키마 주의깊게 보면 쉽게 예측 가능 합니다.




첫 번째 중고거래 게시물.

``` javascript
interface Props {
    articleId: string 
}
```




비지 포스트

``` javascript
interface Props {
    bizPostId: string 
}
```


이런 `기능들은 하나 새로 만들어야 할까 ㄴㄴ `
switch 문을 통해 만들어 주면 됩니다.

``` javascript
{feedItems.map((feedItem) => {
    switch (feedItem.__typename) {
        case 'Article':
            return <CatdArticleFleaMarket articleId={feedItem.id} />
        case 'BizPost':
            return <CardBizPost articleId={feedItem.id} />
        default:
            return null 
    }
})}
```


제품 여기 저거서 article 모델에 접근 할 수 있는 전역 아이디만 있다면?
저희가 만든 중고거래 카드를 재사용 할 수 있게 됩니다.

미래에 일관성 있게 변화. 좋은 경험을 보여 줄 수 있습니다.


정리.

1. 비슷한 관심사라면 가까운 곳에.
2. 데이터를 ID 기반으로 정리하기.
3. 의존한다면 그대로 드러내기.
4. 모델 기준으로 컴포넌트 분리하기.

관점이 기술보다 중요하다.
