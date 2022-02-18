# Coss Conference 

[Javascript Bundle Diet][https://toss.im/slash-21/sessions/3-2]

<h2>Clean code 정리 내용</h2>

왜 번들 사이즈가 중요 한가?

2초 이내 로드시 이탈율은 9% 이지만,
5초 초과시 이탈율은 38% 가 됩니다. 

웹 사이트의 로딩속도는 고객들에게 영향을 주게 됩니다. 

Website Speed 를 보면.
바이트별로 보면, 자바스크립트는 같은 크기의 이미지나 웹포늩보다 브라우저가 처리하는 비용이 많이 듭니다. 

번들 사이즈가 중요할까?
JavaScript 파일과, 이미지 파일을 비교 해 봅시다. 

이미지는 파일을 다운로드한 후 디코딩만 하면 되지만,
Javascript 파일 다운로드 캐싱 -> 컴파일 -> 실행 여러 단계를 거치므로 자바스크립트가 처리 비용이 더 높습니다.

이러므로 자바스크립트 번들 최적화는 매우 중요합니다.


1. 가장 먼저 문제를 찾는 것.

도구 

Webpack

Webpack Analyse (다양한 방법 사용법이 복잡)
Webpack Visualizer (깔끔 시각화, 기능 부족)
Webpack Bundle Analyzer (원인을 빠르게 찾기 위해서 추천)

용량별로 시각화를 해 줍니다.
어떤 라이브러리가 많이 사용되고 있는 지 알 수 있습니다.

용량이 큰 라이브러리는 더 가벼운 라이브러리로 대체.
용도가 겹치는 라이브러리는 하나의 라이브러리로 교체. -> 번들 용량을 줄이는 시작.


번들 아날라이즈를 보면 종종 같은 라이브러리이지만 버전이 다른 경우를 볼 수 있습니다.
이유는 Npm 의 특성.

npm 라이브러리 관계 분석 -> 필요 라이브러리 다운로드합니다.
A,B 라이브러리 사용. 모두 같은 버전의 C 라이브러리를 사용하면 패키지매니저는 A,B,C 모두 다운로드.

그런데 가끔 여러 라이브러리가 같은 라이브러리의 서로 다른 버전을 참조하는 경우도 있습니다.
이런 상황을 Dependency conflict 라고 합니다.

패키지 매니저마다 이 문제를 다양한 방식으로 해결 합니다.
-> 더 높은 버전의 라이브러리 사용.
-> 사용자에게 어느 버전을 설치할 지 확인받는 방식 등이 있습니다.


npm 의 해결책은 다른 패키지매니저와 다르게 tree 구조로 필요한 모두 버전을 받는 것.
한 라이브러리에 디펜던시가 있다면? 또 아래에 node_modules 폴더를 만들어서 그곳에 디펜던시 디펜던시를 저장 해 줍니다.

node 런타임이 상황에 맞게 부모의 node_module ? 자식의 node_module 가져 갈지 적합한 폴더를 잘 선택 해 줍니다.
서로 다른 두 버전이라 할 지라도 모두 번들되어 요청되어 각각 다른 버전의 라이브러리를 사용할 수 있습니다.


npm 방식은 디펜던시를 더 편하게 관리하고,
작성자가 의도한 버전대로 동작하고, 효과를 얻을 수 있음.

하지만 tree 구조로 중복 된 버전을 계속 설치하다 보면 node_module 큰 용량 차지 번들에도 좋지 않음.
과도하게 커지는 문제를 해결하기 위해 npm 라이브러리들이 시멘틱 버전 semver 를 지킨다고 가정 합니다.

semver 를 준수하면 더 높은 버전을 사용해도 문제가 없으니 둘 중 더 높은 버전만 받아 
중복 라이브러리를 줄이고 있습니다.

하지만 이 기능이 항상 잘 작동하는 것은 아닙니다.
npm에서는 dedupe 이란? 중복된 모듈을 줄일 수 있는 기능을 제공 합니다.

dedupe 에서 설치과정에서 못 거친 -> 중복된 라이브러리 버전을 확인하고 적합한 버전으로 통합할 수 있습니다.

그런데 토스팀처럼 패키지 관리에 다른 패키지 매니저인 yarn 을 사용하고 있을 수 있습니다.
yarn 에서는 어떻게?

yarn 은 라이브러리 설치과정에서 완벽히 처리해주기 떄문에 신경 쓸 필요가 없습니다.
그런데 문서와 달리 yarn 도 완벽하지 않습니다.

yarn 을 사용한다면?
yarn dedupelicate 라는 라이브러리를 이용해 중복된 라이브러리를 제거하는 것을 추천.

yarn dedupelicate 는 다양한 전략으로 한 라이브러리로 통일 할 수 있게 해줍니다.


버전이 달라 중복된 라이브러리는 기능을 이용해 줄일 수 있습니다. 

yarn dedupe ...



또 다른 라이브러리의 중복현상은 lodash에서 발견할 수 있습니다.

``` javascript
{
    resolove: {
        alias: {
            'loadsh-es': 'lodash',
            'lodash.get': 'lodash/get',
            'lodash.isplainobject': 'lodash/isPlainObject', 
        }
    }
}
```

다른 서티 파트 라이브러리를 통해 loadsh 가 번들에 포함된 경우가 많이 있으니, 
loadsh 최적화는 유용할 것이라고 생각.

loadsh 에서는 다양한 라이브러리가 있습니다.

cjs 버전의 loadsh, 
esm 버전의 lodash-es 가 있고, 
lodash.get 처럼 각 기능별로도 단독 패키지가 존재합니다.

이런 경우 webpack 의 alias 기능을 이용하면,
이와 같은 라이브러리를 loadsh 만 이용하도록 만들 수 있습니다.

라이브러리 중복을 피했다면?
꼭 필요한 코드만 사용하도록 수정해야합니다.

모든 라이브러리가 이후에 설명할 tree-shaking 을 지원하면 좋겠지만,
그렇지 않은 라이브러들도 있습니다 loadsh가 대표적 


- babel-plugin-transform-imports 를 이용해 사용중인 부분만 가려낼 수 있습니다.

``` javascript
import _ from 'loadsh'
import { add } from 'loadsh/fp'

const addOne = add(1)
_.map([1, 2, 3], addOne) 

// babel-plugin-loadsh
--->

import _add from 'loadsh/fp/add'
import _map from 'loadsh/map' 

const addOne = _add(1) 
_map([1, 2, 3], addOne)
```


loadsh 는 생각보다 용령이 큽니다. `groupBy: 6kB`
shorthands 표현, 캐싱등을 지원하기 떄문. `sumBy(data, 'value')`


더 가벼운 라이브러리는 어떻게 찾을 수 있을까?

참고. webpack 은 브라우저와 node 환경을 최대한 맞추기 위해 필요한 경우 polyfill 추가 할 수 있습니다.
-> polyfill 이 추가될 경우 더 느려지는 경우도 있습니다.

토스의 경우 node-rsa 란 라이브러리를 사용했습니다.
node-rsa 는 Node.js 지원만을 생각하고 만들어진 라이브러리라 crypto, Buffer, big Number 등의 ployfill 이 추가되어 ployfill 용량만 무려 100 kB 넘게 추가되었습니다.

가끔은 이런 폴리필이 있을 수 있어 끄거나, 브라우저를 잘 판단해 주는 라이브러리가 필요 합니다.
create-react-app 은 슬라이드처럼 명시적으로 꺼둔 node polyfill 설정을 가지고 있습니다.


설치 전 라이브러리를 잘 고르고 비교하기 위해서
번들포비아란 웹사이트를 추천 합니다. -> 라이브러리 검색 gizp 용량, 다운로드 속도, 각 버전별 용량을 쉽게 확인 할 수 있습니다.

라이브러리 디펜던시를 미리 확인 할 수 있습니다.
추가로 비슷 기능의 라이브러리 용량을 비교해 주기도 합니다.


특히 유용한 것은? exports analysis 로 tree-shaking 이 됐을 떄
함수별로 얼마나 용량을 차지 할 지 미리 확인 할 수 있습니다.





더 가벼운 라이브러리 만들기. 

번들 사이즈를 위해 고려해야 할 점이 많습니다.
사내 라이브러리를 만들어 많이 고려.

라이브러리 제작자 관점에서 가장 중요한 것은 - tree-shaking 입니다.
불필요한 코드를 정적분석을 통해 제거하는 기술 입니다.

tree-shaking 이 지원된다면? 정말 사용 중인 코드만 번들에 포함.

b만 사용하고 있습니다.
a를 번들에서 뺴도 될까? 

정답은 경우에 따라 다릅니다.
순서나, 호출위치에 따라 동작이 달라지는 경우를 side effect 가 있다고 하는데,
이처럼 tree-shaking 을 하려면? side effect 가 없을 떄만 가능합니다.

번들러는 이러한 side effect 를 얼마나 잘 판단하는지에 따라서 tree-shaking 을 잘할 수도 못할 수도 있습니다.
webpack 은 아직 부족한 tree-shaking 을 보여 줍니다.

webpack 이 이해하는 tree-shaking 가능한 라이브러리를 만드는 건, 
생각보다 까다로울 수 있습니다.


라이브러리 만들 떄엔, webpack 에게 side effect 여부를 알려줘야 합니다.
webpack은 package.json에 sideEffect 를 참고해 어떤 파일에 side effect 가 있는 지를 확인합니다.

이떄 side effect 가 없다면? 오른쪽 코드 처럼 side effect가 없는 라이브러리라고 명시 해 줘야 합니다.

webpack 은 sideEffects 필드가 있을 떄만, 코드를 확인 tree-shaking 시도.
side effect 발견, 분석이 어려운 경우 - tree-shaking 하지 않습니다.

output 하나인 상황에서 문제가 발생 할 수 있습니다.
라이브러리를 만들 떄 rollup 번들러는, 기본적으로 하나의 output 만들어 줍니다.

preserveModules: true 로 해주면? - 오른쪽 처럼 원본 소스코드와 유사한 구조의 output 이 나옵니다.
왼쪽처럼 단일 output 파일의 경우 원본 파일의 일부에 tree-shaking 이 불가능한 코드가 섞여 있다면? 
최종 output 은 한 파일이므로 파일 전체가 tree-shaking 에 실패 할 수도 있습니다.

preserveModules 옵션으로 빌드한다면? 
이런 경우에도 일부 파일만 tree-shaking 에 실패하므로, 문제를 완화 할 수 있습니다.

이 것을 킬 경우 토스 라이브러리 50KB 이상의 효과를 본 사내 라이브러리도 있습니다.
또한 컴파일 툴을 잘 고르는 것도 중요합니다.

webpack 에서 직접 dead 코드를 제거하기도 하지만, 
프로덕션 빌드에서 다른 라이브러리의 도움도 받습니다.

바로 terser 입니다.
terser 도 마찬가지, side effect 유무를 판단하여 코드를 지우는데요,
이떄 terser 판단을 돕는 것이 pure annotaiton 입니다.

슬라이드 처럼 terser 는 pure annotation 주석을 확인하면? 
그 코드는 side effect가 없다고 판단합니다.

그런데 바벨은 pure annotation 을 부여주지만, 아직 TypeScript Compiler 는 붙여주지 않습니다.
babel 을 이용해 컴파일 하는것을 권장.

react 를 사용한다면 특히나 - babel-plugin-transform-react-pure-annotaions 덕분에
pure annotation 이 자동으로 삽입되므로 - react component 를 라이브러리로 만들었다면 특히나 유용합니다.

tree-shaking 을 잘 지원하기 위해선 툴 설정도 유의해야 합니다.



여러분의 라이브러리가 의도와 다르게, side effect 발생한다면? 이를 찾는 것은 까다롭습니다.
현재 가장 좋은 방법은 webpack 의 stats 기능을 이용하는 것 입니다.

webpack-cli 를 쓰시는 분이라면? stats 옵션을 이용하고, next.js 별도.






이제 마지막으로 라이브러리 용량을 더이상 줄이기 어렵다면?
무거운 라이브러리의 영향을 최소화하는 것이 중요합니다.

모든 페이지의 용량이 한 번에 증가합니다.
토스 페이지에서 @nivo/pie 라이브러리를 사용하였으나,
모든 페이지에서 50kb 가량의 용량이 증가하는 문제가 있었습니다. 

해결하기 위해 청크로 나누기. 

1. react, react-dom, next 라이브러리가 들어 있습니다.
2. 모든 페이지를 코드를 모아둔 commons 청크. 

- 이 두 개는 모든 페이지에 반영이 되고, 먼저 진입한 페이지에서 캐싱되어, 효율적 리소스 관리가 가능합니다.
2 페이지 이상 공유하는 코드를 모아둔 shared AB 청크.

마지막으로 특정 페이지에서만 사용하는 Chunk 가 있습니다.

청크를 나누면 무거운 라이브러리를 사용하더라도 라이브러리 사용하는 페이지에서만 받기 떄문에, 
영향을 최소화 할 수 있습니다.

또한 페이지를 이동할 떄 추가로 필요한 청크만 받을 수 있는 장점도 있습니다.

webpack magin comment 을 이용하면 prefetch 기능을 이용할 수 있다는 점 입니다. 
prefetch 를 이용하면, 여유로운 시간에 미리 받을 수 있으므로 사용자에게 최소한의 지연시간을 줄 수 있습니다.

``` javascript
import dynamic from 'next/dynamic';

const PageA = dynamic(() => import('./PageA'));
const PageB = dynamic(() => /* webpackPrefetch: true */ import('./PageB')); 
```

이와 같은 방식으로 토스팀에서는 최대 1/3 용량을 가진, 
번들을 만들 수 있었고, 적절한 DI와 Prefetch 를 이용해, 초기 로딩 속도를 절반 가까이 줄일 수 있었습니다.

