# 토스 컨퍼런스. 

[Mico-frontend React, 점진적으로 도입하기][https://toss.im/slash-21/sessions/3-1]

<h2>Mico-frontend React, 점진적으로 도입하기</h2>

## 발표 : 조유성 Full Stack Developer 

토스팀에서는 React 비율이 높아짐에 따라 -> `마이크로 프론트엔드 아키텍쳐`를 선택 하게 되었습니다.
그 이유와 후기를 공유합니다.

그리고 `긴 빌드 시간 문제`를 어떻게 효율적으로 처리햇는 지 설명드리겠습니다.

토스 인터널에서 공문 작성 -> 또다른 UI 가 필요하다 여겨져 -> `부분적 몇 개를 리액트`로 만들자고 하자.
당시에는 create-react-app 프론트엔드 프로젝트를 빠르게 셋업.

-> 적은 비용으로 바르게 실험을 진행하고자 create-react-app 를 선택하게 되었습니다.

설정을 해보니 완전한 `SPA` 가 아니라 장고와 함께 사용하는 부분에서는 `create-react-app` 과 맞지 않아 문제점이 발생했습니다.
그러다보니 create-react-app 다 이해한 후 필요한 부분을 다시 패치를 적용해야 하니 장기적으로 볼 떄 `from scratch`로 빌드 툴링을 하는 것이 낫겠다고 생각 하게 되었습니다.

webpack 빌드의 결과물을 쉽게 Django 템플릿에 삽입하기 위해, 추가적으로 2가지 서드파티 라이브러리를 사용했습니다.

1. webpack-bundle-tracker 

webpack 빌드 결과물 chunk 정보를 `JSON` 파일에 추출해주고

2. django-webpack-loader 

JSON 파일의 정보 바탕으로, 적절한 `<script>` 태그나 `<link>` 태그를 생성해주는 원리입니다.

각 서비스는 `webpack entrypoint` 로 구분되지만,
하나의 패키지에서 하나의 `webpack` 설정으로 `한번에 빌드`되는 구조를 갖게 되었습니다.

서로 다른 서비스들이 `하나의 webpack 빌드`로 묶이게 되자, 새로운 문제가 발생했습니다.
-> `의존성 관리`가 매우 힘들어졌습니다. 

서로 코드 공유 x A,B 서비스가 있습니다.
두 서비스가 공유하는 X라는 `의존성 패키지`가 있습니다.

A -> 개발 완료 잘 작동.
B -> 개발하다 보니 A가 쓰고 있던 X에서 버그가 발생했습니다.

X의 시멘틱 버저닝이 잘 지켜진다면 문제가 없었겠지만,
현실적으로 B 개발을 위해 X의 패치 버전만 올려도, 
A의 작동이 바뀌거나, 빌드 실패하는 경우가 종종 있었습니다.


긴 빌드 시간도 문제가 있었습니다.

A의 코드 한 줄만 수정해도 B부터 Z 서비스까지 모두 새로 빌드해야 하니 빌드 시간이 길어집니다.
개발자 로컬 환경에서 페이지 변경까지 수 초 이상 발생합니다.
-> 이런 문제는 개발 생산성에 문제점을 주므로 꼭 해결해야 합니다

생겨난 것이 마이크로 프론트엔드.



기존의 거대한 `소스코드를 독립적 패키지`로 분리하고 각각을 빌드했습니다.
패키지는 유지보수가 이루어지는 단위를 기준으로 분리했고, `3가지 유형으로 구분.`

1. 인프라 패키지 (동일 빌드 툴링 공유) 
2. 라이브러리 패키지 (공통 소스코드 관리) 
3. 서비스 패키지 (페이지에서 독립적으로 작동) 

어떻게 구현했을까요?

처음에는 Yarn Workspace + Lerna 
현재 구현 Yarn 2 Workspace Plugin 

-> 각 패키지별로 독립적 의존성을 갖게 되면서, 앞서 설명한 의존성 문제를 해결 가능.
코드 변경이 갖는 영향의 범위를 패키지별로 강력하게 격리할 수 잇게 되면서, 사소한 버그가 제품 전체에 미치는 영향도 줄일 수 있었습니다.
빌드 속도도 줄였습니다.



마이크로 프론트엔드 아키텍처를 도입하면서.
-> 다른 전략을 갖고 개발되는 제품들이 다르게 개발될 수 있었기 떄문입니다.

즉 제품 전략 면에서 독립된 제품들이 기술적으로도 독립된 것 입니다.



빌드 시간도 줄이는 가장 좋은 방법은? 

즉 - `빌드를 하지 않는 것.`
어떻게 빌드를 하지 않을 수 있을까요?

`소스 코드가 바뀐 패키지만` 빌드하고,
나머지는 기존 빌드 결과물을 재사용.

이런 방식을 토스팀에서는 `zero-build` 라고 부르고 있습니다.



소스코드가 바뀌엇다는 것은 어떻게 알 수 있을까?
git을 활용하면 쉽습니다.

git의 커밋 ID는 그 커밋에 포함된 모든 파일의 해시를 누적시킨 결과와 같습니다.
각각 파일은 Git 내부에서 blob 처리되고,
각각의 blob 에서 해쉬를 계산, 각각 폴더는 tree로 처리.

tree의 해시는 그 tree에 속한 blob 의 모든 해시 값의 해시입니다.
이런 식으로 반복하면 커밋의 해시가 결정됩니다.

그렇기 떄문에 특정 tree가 변경되었는 지 알고 싶다면?
이전 커밋 해시와 현재 커밋 해시를 비교하면 됩니다.

나머지는 기존 빌드 결과물을 재사용한다고 했는데.
-> 그 기존 결과 빌드 결과물은 어디에 저장할까요?

토스팀에서는 Git 저장소에 함께 저장합니다.
개발자가 저장소를 pull를 받기만 하면, 최신 빌드가 이미 다운로도 되어있습니다.

물론 빌드 결과물을 재사용한 것은 아니고, 압축해서 사용했습니다.



이 방식은 Yarn2 의 `zero-install` 이 작동하는 방식과 비슷합니다.
-> 빌드 속도가 최대 15배까지 빨라지는 것을 볼 수 있습니다.
