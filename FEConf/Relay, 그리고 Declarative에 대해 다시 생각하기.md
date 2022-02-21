# Relay, 그리고 Declarative에 대해 다시 생각하기

[Relay, 그리고 Declarative에 대해 다시 생각하기][https://www.youtube.com/watch?v=YP7d9ae_VzI]
[원지혁 Github][https://github.com/tonyfromundefined]

<h2>Relay, 그리고 Declarative에 대해 다시 생각하기 정리 내용</h2>

## 발표 : 프론트엔드 개발자 원지혁 @당근마켓

페이스북에서 만든 Relay 설명,
직접 접근하면서 꺠닭은 점에서 설명.

좋은 클라이언트 좋은 제품 좋은 코드에 대해 얻어 갈 수 있습니다.


<h3>데이터/뷰 일관성과 전역 상태 관리</h3>

클라이언트에서의 일관성

CI 작업툴이 시작되면 사이드바에 생기고.
클릭을 하면 어떤 스탭이 진행 중인 지 알 수 있습니다.
근데 스탭에서 하단에 완료됨이라고 나오네요.

즉 일관되지 않음.
이런 것을 데이터/뷰 일관성이라고 합니다.



### 전역 상태 관리

보여지는 컴포넌트에서 하나의 상태를 바라보므로, 어떤 변화가 일어났을 떄 일관성을 지킬 수 있습니다.
-> 전역 상태 관리를 사용, 데이터 중복을 최소화하기.


데이터 정규화 -> (주어진 데이터 중복된 데이터 삭제하는 것을 말함) 

JSON 예시, 전역 상태 저장하게 되면
3723, 20901 코멘트가 중복되서 저장되는 문제가 발생합니다.

이것을 정규화 합니다.

``` javascript
{
    "id": 12469,
    "name": "Tony",
    "articles": [
        {
            "id": 3723,
            "title": "Hello, Undefined",
            "comments": [
                {
                    "id": 20901,
                    "content": "Welcome to FEConf" 
                }
            ]
        }
    ]
}
```

데이터 정규화를 하게 되면.

``` javascript
{
    "id": "User#12469",
    "name": "Tony",
    "articles": [
        "Article#3723"
    ]
}

{
    "id": "Article#3723",
    "title": "Hello, Undefined!",
    "comments": [
        "Comment#20901"
    ]
}

{
    "id": "Comment#20901",
    "content": "Welcome to FEConf"
}
```


문제 1. 데이터 정규화는 힘들다.

힘들게 JSON 을 받아왔는데, 이것을 또 정규화해야합니다.
JSON -> User, Article, Comment 






2. 컴포넌트의 데이터 의존성.

컴포넌트에 그릴 떄 코드적인 문제에 대해 이야기.

컴포넌트 트리

모델 Channel, User, Article, Comment, Message ... 다 엮여서 있습니다.
JSON 문서에 비대해 지고, 컴포넌트를 여러개로 분리하게 됩니다.

분리된 컴포넌트에 Props 형태로 필요한 것들을 주입하게 됩니다.



문제 2. 컴포넌트의 데이터 의존성.

Article#1 -> <Article /> -> <Title /> <Content />

Article 안에 재목을 보여주는 박스 Text 박스이긴 하지만, Article 간적접으로 의존하고 있다고도 볼 수 있습니다.
잘 쪼개 놓였어더.. `결국 Article 모델이 바뀌면? <Artlcie />뿐 아니라 하위 컴포넌트들도 확인이 필요`








###  그래프QL, 그리고 릴레이.

- 두 문제를 이 2개를 사용하여 쉽게 해결 할 수 있습니다.

API를 그래프로 표현하기 : 그래프QL 

약속=언어
그래프 QL은 이러한 문제를 해결하기 위해 개발된 언어(약속) 

약속을 정한다 -> 약속을 통한 쉬운 정규화 -> 더 쉬운 상태 관리.



1. 타입 시스템을 통한 자연스러운 데이터 정규화.

모델 정의를 도와줍니다.
각 모델로 정규화 할 수 있는 기반을 만들어 줍니다.

JSON 

``` javascript
type User {
    id: ID
    name: String
    articles: [Article]
}
// User 

type Article {
    id: ID
    title: String
    comments: [Comment]
}
// Article 

type Comment {
    id: ID
    content: String 
}
// Comment 
```



1. ID 값을 통해 자동적으로 데이터/뷰 일관성 달성하기.

``` javascript
// id 필드를 통해 그래프QL 클라이언트가 자동으로 상태 관리.

type Job {
    id: ID,
    name: String,
    status: JobStatus 
}

// 그래프QL 클라이언트 (Job#72)
// 작업 72 (완료됨) 
// 작업 72 
```

id 필드를 통해 그래프QL 클라이언트가 자동으로 상태 관리.
데이터/뷰 일관성을 위해 따로 리소스가 필요 없음.



프래그먼트.

Fragment - 데이터 의존성 조각. 

``` javascript
<Article />
-> <Title /> // Article 모델의 title 필드가 필요. 
-> <Content /> // Article 모델의 content 필드가 필요. 
// 컴포넌트는 모델에 포함된 값을 필요로 한다.
```

``` javascript
<Title /> // Article 모델의 title 필드가 필요. 

// -> 
fragment FragmentName on Article {
    title 
}
```

- 컴포넌트의 데이터 의존성을 표현하는 문법.
- UI를 그리는 데 필요한 데이터들의 집합을 프래그먼트들의 집합으로 표현 가능.






코-로케이션

Co-location - 컴포넌트와 프래그먼트를 같은 파일에 작성하기.

<Title /> <Fragment>


``` javascript
const Title = (props) => {
    const article = useFragment(graphql`
    fragment Title_article on Article {
        title
    }`, props.article)

    return (
        <div>
            {article.title}
        </div>
    )
}
```






잘 분리된 컴포넌트.

각 컴포넌트가 어떤 모델의 어떤 필드를 의존성으로 가지는 지를 명확하게 확인할 수 있습니다. 
따라 이제는 컴포넌트가 무슨 데이터를 띄우지? 각 컴포넌트 파일 하나만 확인하면 어떤 파일 의존성을 가지고 있는 지, 어떤 것을 표현하는 지를 알 수 있습니다.


``` javascript
1. fragment Articles_user on User {
    articles {
        ...Article_article
    }
}

2. fragment Article_article on Article {
    id
    ...Title_article
}

3. fragment Title_article on Article {
    title 
}
```

쿼리 (단일 네트워크 요청)

프래그먼트 -> 프래그먼트 -> 프래그먼트 이런 식.
런타임은 해당 런타임을 실행하고 응답을 정규화 해서 해줍니다.


npm install relay-runtime react-relay 
npm install --dev relay-compiler relay-config babel-plugin-relay 
npm add --dev @types/relay-runtime @types/react-relay relay-compiler-language-typescript 





4. 적용하기 - 내 근처 탭.
   
- 로컬 비즈니스 허브.
- 우리 동네 근처 가게, 정보를 보여주고 검색할 수 있는 서비스.

한 페이지에 여러 JSON 을 가져오게 됩니다. -> 동네 업체, 구인구직, 부동산, 지역광고. -> 이미지, 유저, 검색.
여러 개 REST API 를 GraphQL 을 이용해서.

서버 레이어를 증가시키면, 네트워크 지연시간, 디버깅의 어려움 등 다양한 문제 발생.
클라이언트 스키마 - 그래프 QL 서버 코드를 클라이언트내에 포함하기.

적용해보면서 느낀점

단점 : 워크플로우가 복잡해짐.
    -> 그래프QL 스키마 수정 -> 타입스크립트 타입 생성 (그래프QL 코드 제너레이터) -> 그래프QL 리졸버 수정 -> 컴포넌트 수정 -> 릴레이 컴파일 -> 결과.
    -> 타입 선언을 이중으로 해주어야 합니다.
    -> 번들사이즈 증가.

장점 : 프래그먼트 코-로케이션을 통해 유지보수성이 높아짐.
    -> 데이터를 어떻게 처리하느냐 -> 어떻게 선언하느냐 
    -> 컴파일 타임에 오류를 미리 알 수 있음.
    -> 데이터 관련한 버그를 현저히 줄어듬.
    -> 이상한 동작은 릴레이가 전부 잡아줍니다.
    예: 페이지네이션 결과에서 같은 아이템이 중복해서 내려오면? 중복된 아이템을 릴레이가 자동으로 삭제해 줌.





5. 선언적으로 작성한다는 것.

- 패턴을 통해 깨닫고 배운 부분을 공유.

일정 스크롤 이상일 떄 특정 상태를 true 로 바꾸기.

``` javascript
import React, { useEffect, useState } from 'react';

const HelloFEConf: React.FC = () => {
    const [scrolled, setScrolled] = useState(false)

    useEffect(() => {
        const onScroll = () => {
            if (window.scrollY > 500) {
                setScrolled(true) 
            } else {
                setScrolled(false)
            }
        }
        window.addEventListener('scroll', onScroll)

        return () => {
            window.removeEventListener('scroll', onScroll)
        }
    }, [setScrolled])

    return <div>...</div>
}
```


리팩토링 한다면?
커스텀 훅으로 스크롤 처리 관련 로직을 분리.

``` javascript
const HelloFEConf: React.FC = () => {
    const scrolled = useScrolled()

    return <div>...</div>
}
```

하지만 scrolled 가 무슨 기능을 하는 지 도저히 알 수 없습니다.

Relay는 컴포넌트마다 필요 데이터를 근처에 선언하기 위해 만들어졌습니다.
이것은 `어떻게` 데이터를 가져올지에 대한 걱정을 하지 않고 각 컴포넌트마다,
`어떤` 데이터가 필요하지만 선언하는 방식을 말합니다.


How와 What 으로 코드를 바라보기.

``` javascript
import React, { useEffect, useState } from 'react';

const HelloFEConf: React.FC = () => {
    const [scrolled, setScrolled] = useState(false) // What 

    useEffect(() => { // How 
        const onScroll = () => {
            if (window.scrollY > 500) { // What 
                setScrolled(true) 
            } else {
                setScrolled(false) // What 
            }
        }
        window.addEventListener('scroll', onScroll) // How 

        return () => {
            window.removeEventListener('scroll', onScroll) // How 
        }
    }, [setScrolled])

    return <div>...</div>
}
```

이제 리팩토링을 해 봅시다.

How와 What 으로 분리하기.
How 같은 경우네느 listener.


How 

``` javascript
function useScrollEffect(
    listener: (scrollY: number) => void, 
    deps?: any[]
) {
    useEffect(() => {
        const onScroll = () => {
            listener(window.scrollY)
        }
        window.addEventListener('scroll', onScroll)

        return () => {
            window.removeEventListener('scroll', onScroll)
        }
    }, deps)
}
```


What 

``` javascript 
const HelloFEConf: React.FC = () => {
    const [scrolled, setScrolled] = useState(false) 

    useScrollEffect(
        (scrollY) => {
            if (scrollY > 500) {
                setScrolled(true) 
            } else {
                setScrolled(false) 
            }
        }, 
        [setScrolled]
    )
    return <div>...</div>
}
```



How 는 NPM 라이브러리처럼.

- 범용적으로 사용될 수 있도록 설계하기.
- 유닛 테스트를 촘촘하게.


What은 연관된 것끼리 가까운 곳에 모으기.

- 어떤 컴포넌트인지, 어떤 의존성을 가지고 있는 지 한 눈에 알 수 있도록.

