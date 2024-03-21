# Component, thinking again

[Component, thinking again][https://www.youtube.com/watch?v=HYgKBvLr49c&t=1255s]

## 1. 비슷한 관심사는 가까이 두기

- 목적: 코드의 집중도를 높이고, 관련된 코드끼리 쉽게 찾아볼 수 있도록 하기 위함입니다.
- 실천 방법: 로직과 스타일을 같은 위치에 두거나, 필요시 같은 폴더 내에 분리된 파일로 관리합니다.
- 이점: 여러 파일을 오가며 코드를 수정할 필요가 줄어들어, 효율적인 코드 관리가 가능해집니다.

## 2. 데이터 ID 기반으로 정리하기

- 목적: 데이터 구조를 간결하게 하여 데이터를 쉽게 관리하고 접근할 수 있게 합니다.
- 실천 방법: normalizr 같은 라이브러리를 사용해 데이터를 정규화하고, 모델명과 ID를 기준으로 데이터를 조직합니다.
- 이점: 중복을 줄이고, 데이터 관계를 명확하게 표현하여 데이터 관리의 복잡성을 낮출 수 있습니다.

```javascript
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

```javascript
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

## 의존성 줄이기

- 목적: 컴포넌트 간의 결합도를 낮추어, 각 컴포넌트를 독립적으로 유지보수할 수 있게 합니다.
- 실천 방법: Props 대신 커스텀 훅을 사용하여 데이터를 관리하고, 전역 ID를 활용해 필요한 데이터만을 가져옵니다.
- 이점: 컴포넌트 간의 의존성이 줄어들어, 변경 사항이 다른 컴포넌트에 미치는 영향을 최소화할 수 있습니다.

```javascript
import { useNode } from "~/store";

interface Props {
  articleId: string;
}
const Something: React.ExoticComponent<Props> = (props) => {
  const article = useNode({ on: "Article" }, props.articleId);

  return (
    <div>
      <h1>{article.title}</h1>
    </div>
  );
};
```

## 이름 짓기

- 목적: 코드를 이해하기 쉽게 하고, 컴포넌트의 역할을 명확히 표현하기 위함입니다.
- 실천 방법: Props 네이밍을 통해 컴포넌트가 기대하는 데이터 구조를 명확하게 표현하고, 모델 기반으로 컴포넌트를 구분합니다.
- 이점: 코드의 가독성이 향상되고, 컴포넌트의 목적과 사용 방법을 쉽게 파악할 수 있습니다.

```javascript
interface Props {
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

```javascript
interface Props {
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

interface Props {
    image: {
        thumbnailUrl: string
    }
}
```

## 재사용성 높이기

- 목적: 코드 변경 시의 편의성을 위해 컴포넌트를 재사용 가능하게 만듭니다.
- 실천 방법: 변화가 예상되는 부분은 독립된 컴포넌트로 분리하고, 모델 기준으로 컴포넌트를 분리합니다.
- 이점: 코드의 중복을 줄이고, 유지보수를 용이하게 하여 개발 효율성을 향상시킵니다.

```javascript
{
  feedItems.map((feedItem) => {
    switch (feedItem.__typename) {
      case "Article":
        return <CatdArticleFleaMarket articleId={feedItem.id} />;
      case "BizPost":
        return <CardBizPost articleId={feedItem.id} />;
      default:
        return null;
    }
  });
}
```
