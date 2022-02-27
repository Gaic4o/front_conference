# 그런 REST API로 괜찮은가

[그런 REST API로 괜찮은가](https://www.youtube.com/watch?v=RP_f5dMoHFc)
발표자 : 토스 이응준 서버 개발자.

## REST 

뭔가 미묘하게 잘 모르겠는 주제.
우리가 개발을 하거나, `코드 리뷰를 하거나 그 부분은 뭔가 REST 스럽지 않은 거 같아요.`

문제는 정확히 `REST` 가 뭔지 말하기 어렵습니다. 
잘 설명이 안되고 이해도 안되고, 그런 건 REST 가 아니라고 반박 해주고 싶은 데 뭔가 반박하기 어렵습니다.

REpresentational 
State 
Transfer 

위키백과를 보면 - REST 는 컴퓨터 상호 운영성 중 하나. 
어떤 계기로 나왔는 가? 역사를 따라 가 봅시다.

시작은 웹 WEB (1991) Q: 어떻게 `인터넷에서 정보를 공유할 것 인가?` 
-> 해답으로 웹이 나오게 됐습니다.

A : 정보들을 하이퍼텍스트로 연결한다.

    표현 형식 : `HTML`
    식별자 : `URI`
    전송 방법 : `HTTP`

`HTTP 프로토콜`을 여러 사람들이 설계하게 됐습니다.
로이필딩(Roy T. Fielding) 이라는 사람이 이 연구에 참여하게 되면서 `1994 년에 HTTP 1.0 작업`을 하게 됨.

이미 그전에 전 세계에 웹 서버들이 존재 했습니다.

로이필딩은 이 많은 `웹 서버들을 어떻게 명세를 더하고,` 기능을 더 하는 상황에 놓이게 됐습니다.
HTTP 프로토콜을 붙이게 되면 그 전에 만들어 놓은 서버와 호환성이 안 될 것을 압니다.

어떻게 하면 웹을 망가트리지 않고 HTTP 을 넣을 수 있을까? 
-> 해결책 : HTTP Object Model 

REST (1998) 
Roy T. Fielding, Microsoft Research 에서 발표.

`Representational State Transfer`

그리고 2년 후에 로이필딩은 -> 박사 논문으로 발표.

`Architectural Styles and the Design of Network-based Software Architectures`
박사 논문이 우리가 알고 있는 REST 정의입니다. (약 120페이지 논문) 




API 만들어 지게 됐습니다.

XML-RPC(1998) 
by Microsoft -> `SOAP`

처음에는 `마이크로소프트에서 원격으로 다른 시스템 메소드를 호출 할 수 있는 프로토콜`을 만듭니다.
-> 나중에 SOAP 으로 바뀝니다.

Salesforce API (2000. 2) 에서 API를 공개.

어떤 아이디를 Object 를 가져오는 API 인데도 분량이 너무 많음.
굉장히 복잡합니다. -> 잘 인기가 없었음 (너무 복잡해서) 

``` html
<?xml version="1.0" encoding="utf-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"xmlns:urn="urn:enterprise.soap.sforce.com">
    <soapenv:Header>
        <urn:SessionHeader>
            <urn:sessionId><b>OQWRQWRJWJ@!44242424</b></urn:sessionId>
        </urn:SessionHeader>
    </soapenv:Header>
    <soapenv:Body>
        <urn:fieldList><b>Id, Name, Website</b></urn:fieldList>
        <urn:sObjectType><b>Account</b></urn:sObjectType>
        <urn:ids><b>001D00000HS2Su</b></urn:ids>    
    </soapenv:Body>
</so...>
```


4년 후에 또 flickr 에서 API가 나옵니다 (2004, 8)

SOAP 

- 복잡하다.
- 규칙 많음.
- 어렵다.

``` html
<?xml version="1.0" encoding="utf-8"?>
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope">
    <s:Body>
        <s:Fault>
            <faultcode>flickr.error.[error.code]</faultcode>
            <faultstring>[error.message]</faultstring>
            <faultactor>http://www.flickr.com/services/soap/</faultactor>
            <details>Please see thttp://ww...</details>
        </s:Fault>
    </s:Body>
</s:Envelope>
```

REST (일단 같은 일을 하는 API 인데 SOAP 에 비해서 훨씬 짧습니다.)

- 단순하다.
- 규칙 적음
- 쉽다.

``` html
<?xml version="1.0" encoding="utf-8"?>
<rsp stat=s"fail">
    <err code="[error.code]" msg="[error.message]">
</rsp>
```


SOAP API < `REST API` 인기도 

2006년 : 아마존 AWS 85% REST API.
2019년 : Saleforce.com REST API 추가.



그런데? 

CMIS (2008) 

- CMS 를 위한 표준.
- EMC, IBM, Microsoft 등이 함께 작업.
- REST 바인딩 지원.

이걸 본 로이필딩이 이런 말을 합니다 -> REST 가 없다.
만든 저자는 REST 가 아니라고 합니다.


그리고 2016년에 마이크로소프트 REST API 가이드라인을 만듭니다 

- uri는 https://{serviceRoot}/{collection}/{id} 형식이어야 합니다.
- GET, PUT, DELETE, POST, HEAD, PATCH, OPTIONS 를 지원해야 합니다.
- API 버저닝은 Major.minor 로 하고 uri에 버전 정보를 포함시킨다.
- 등등.

사람들이 보기엔 되겐 알고 있는 REST API 좋은 잘 부합한다고 합니다.
하지만 로이필딩이 또 REST API 가 아니고 HTTP API라고 해야 합니다.

사람들이 알고 있던 REST API하고 - 로이필딩이 말하는 REST API 가 너무 많이 차이가 났습니다.
뭐가 문제인 걸까요?

로이필딩 박사 논문 : REST 가 뭐냐? - 분산 하이퍼미디어 시스템 (예: 웹)을 위한 아키텍쳐 스타일.
아키텍쳐 스타일이 뭔가? (제약조건의 집합) 

제약조건의 집합을 모두 지켜야 REST 따라야 한다고 말합니다.
6개 아키 텍쳐 스타일로 이루어져 있습니다. 

1. client-server
2. stateless
3. cache
4. uniform interface 
5. layered system
6. code-on-demand (optional) 


현재의 REST API를 만들 떄 이미 `HTTP 만 잘 따라도 잘 지킬 수 있습니다.`
uniform interface 라는 것을 잘 만족하지 못합니다.



Uniform Interface 의 제약조건.

1. identification of resources (URL 식별) 
2. manipulation of resources through representations (리소스를 만들 거나, 업데이트 하거나, 삭제 하거나 할 떄 REST 메시지 포함 담아 보내 달성 대체로 잘 달성) 

거의 모든 REST API 현재 모든 것들이 지키지 못하고 있습니다.

3. `self-descriptive messages` (메시지 스스로 설명해야 합니다.) 

GET / HTTP/1.1 

(이 HTTP 요청 메시지는 뭔가 빠져있습니다. 어디 목적지로 가는 지 빠져있습니다. Host: www.example.org)
즉 Host 가 빠져있으므로 self-descriptive 하지 않다라고 합니다.

Content-Type 이 만드시 들어가야 합니다.
문자들이 어떤 Type인지 보고 이해하게 되고 파싱을 할 수 있게 됩니다.

하지만 이것을 안다고 해서 op, path 가 뭐를 의미하는 지 알 수 없습니다.

Content-Type: application/json-patch+json -> 미디어 타입 명세를 찾아가서 json patch 이해 한 다음에, 메시지를 해석 하면 옳바르게 메시지 의미를 이해 할 수 있게 됩니다.

``` html
HTTP/1.1 200 OK 
Content-Type: application/json-patch+json 

[ { "op": "remove", "path": "a/b/c" } ]
```




4. `hypermedia as the engine of application state (HATEOAS)` (애플리케이션의 상태는 Hyperlink를 이용해 전이되어야 합니다.)

HTML 같은 경우 HATEOAS 를 만족하게 됩니다.
a 태그를 통해 하이퍼링크가 나와 있고 그 다음 상태로 전이가 가능 하기 떄문에 HATEOAS 만족합니다.

``` html
HTTP/1.1 200 OK
Content-Type: text/html

<html>
<head></head>
<body><a href="/test">test</a></body>
</html>
```

JSON 에서도 만족할 수 있습니다.
link 라는 태그가 다음 메시지를 가리킬 수 있는 게시물을.
이전 게시물의 url, 그 다음 url 정보를 표현.

``` json
HTTP/1.1 200 OK
Content-Type: application/json
Link: </articles/1>; rel="previous",
      </articles/3>; rel="next";
    {
        "title": "The second article",
        "contents": "blah blah.."
    }
```



왜 Uniform Interface ? 

독립적 진화.

- 서버와 클라이언트가 각각 독립적으로 진화한다. (서버 클라이언트가 독립적으로 진화 서버 API 변경, 삭제 -> 클라이언트가 업데이트 할 것이 하나도 x) 
- `서버의 기능이 변경되어도 클라이언트를 업데이트할 필요가 없다.` 
- REST를 만들게 된 계기 : "How do I improve HTTP without breaking the Web" 
  

웹 

- 웹 페이지를 변경했다고 웹 브라우저를 업데이트할 필요는 없다.
- 웹 브라우저를 업데이트 했다고 웹 페이지를 변경할 필요도 없다.
- HTTP 명세가 변경되어도 웹은 잘 작동합니다.
- HTML 명세가 변경되어도 웹은 잘 작동합니다.



어떻게 한걸까요?

놀라운 마법으로 한방에 해결 (X)
피땀흘려 노력함 (o) 


이 분들이

W3C Working groups
IETF Working groups 
웹 브라우저 개발자들
웹 서버 개발자들


이 정도의 노력을 합니다.

HTML5 첫 초안에서 권고안 나오는 데 까지 6년.
HTTP/1.1 명세 개정판 작업하는 데 7년.
-> 하위 호환성을 꺠트리면 안됨. 상당히 한 주제에 많은 토론을 할 수 밖에 없습니다.


상호 운영성 (Interoperability) 에 대한 집착

Referer 오타지만 안 고침.
charset 잘못 지은 이름이지만 안 고침.
HTTP 상태 코드 416 포기함 (I`m a teapot) 
HTTP/0.9 아직도 지원함 (크롬, 파이어폭스)


그런 노력이 없다면 웹도 ...
-> Netscape 6.0은 지원하지 않습니다라는 팝업을 보게 됩니다.

웹 서버가 브라우저 호환성을 고려하지 않고 작업 할 떄 발생하는 팝업.
만약에 많은 분 들이 REST 만드는 분들이 많은 노력을 해주지 않으면 일상 다반사에서 맨날 봤어야 합니다.
-> 이런 것은 이제 보기 힘듭니다, 수 많은 분들이 피땀 눈물을 흘려서 만들어 냈습니다.





REST 가 웹의 독립적 진화에 도움을 주었나

- HTTP 에 지속적으로 영향을 줌.
- Host 헤더 추가. 
- 길이 제한을 다루는 방법이 명시 (414 URI Too Long 등) 
- URI 에서 리소스의 정의가 추상적으로 변경됩니다 : 식별하고자 하는 무언가
- 기타 HTTP 와 URI 에 많은 영향을 줌.
- HTTP/1.1 명세 최신판에서 REST에 대한 언급이 들어갑니다.
- Reminder : Roy T. Fielding 이 HTTP와 URI 명세의 저자 중 한 명입니다.


그럼 REST 는 성공했는가?

- REST 는 웹의 독립적 진화를 위해 만들어졌습니다.
- 웹은 독립적으로 진화하고 있다. 


그런데 REST API 는 ?

- REST API 는 REST 아키텍쳐 스타일을 따라야 합니다.
- 오늘날 스스로 REST API 라고 하는 API들의 대부분이 REST 아키텍쳐 스타일을 따르지 않습니다.


REST API도 저 제약조건들을 다 지켜야 하는 건가?

- 로이필딩은 REST API 라고 하는 것은 하이퍼텍스트를 포함한 self-descriptive 한 메시지의 uniform interface 를 통해 리소스에 접근하는 API  



원격 API가 꼭 REST API여야 하는 건가? 

- 아니라고 합니다. (시스템 전체를 통제할 수 있다고 생각하거나, 진화에 관심이 없다면 -> REST 에 대해 따지느라 시간을 낭비하지 마라)
만약 서버 개발자가 클라이언트 개발자을 통제를 가능 하다?, REST 클라이언트 둘다 다 만들면 굳이 REST를 따를 필요가 없습니다.

진화에 관심이 없다는 것은 - 최근 10동안 업데이트 했다. -> 이것이 진화에 관심이 없다는 뜻.


그럼 이제 어떻게 할까?

1. REST API 를 구현하고 REST API 라고 부른다.
2. REST API 구현을 포기하고 HTTP API 라고 부른다.
3. `REST API 가 아니지만, REST API라고 부른다.` (현재 상태) 



비교 

        흔한 웹 페이지   HTTP API 
Protocol   HTTP         HTTP 
커뮤니케이션  사람-기계      기계-기계
Media Type HTML         JSON 


            HTML 과         JSON 을 비교.
Hyperlink   됨 (a태그 등)        정의 x
Self-descriptive 됨 (HTML 명세)  불완전* 



비교 

HTML 

``` html
GET /todos HTTP/1.1 
Host: example.org 

HTTP/1.1 200 OK 
Content-Type: text/html

<html>
    <body>
        <a href="https://todos/1">회사 가기</a>
        <a href="https://todos/2">집에 가기</a>
    </body>
</html>
```

Self-descriptive 

1. 응답 메시지의 Content-Type 을 보고 media type 이 text/html 임을 확인.
2. HTTP 명세에 media type 은 IANA 에 등록되어있다고 하므로, IANA 에서 text/html 의 설명을 찾는다.
3. IANA 에 따르면 text/html의 명세는 http://www.w3.org/TR/html 이므로 링크를 찾아가 명세를 해석한다.
4. 명세에 모든 태그의 해석방법이 구체적으로 나와있으므로 이를 해석하여 문서 저자가 사용자에게 

HATEOAS 

- a 태그를 이용해 표현된 링크를 통해 다음 상태로 전이될 수 있으므로 HATEOAS 를 만족합니다.





``` json
GET /todos HTTP/1.1 
Host: example.org 

HTTP/1.1 200 OK
Content-Type: application/json 

[
    {"id": 1, "title": "회사 가기"},
    {"id": 2, "title": "집에 가기"}
]
```

Self-descriptive 

1. 응답 메시지의 Content-Type 을 보고 media type이 application/json 임을 확인한다.
2. HTTP 명세에 media type 은 IANA 에 등록되어있다고 하므로, IANA 에서 application/json 의 설명을 찾는다.
3. IANA 에 따르면 application/json의 명세는 draft-left-jsonbis-rfc7159bis-04 이므로 링크를 찾아가 명세를 해석한다.
4. 명세에 json 문서를 파싱하는 방법이 명시되어있으므로 성공적으로 파싱에 성공, 그러나 "id" 가 무엇을 의미, "title" 이 무엇을 의미하는 지 알 방법 x -> FAIL 

HATEOAS 

- 다음 상태로 전이할 링크가 없다 FAIL 




Self-descriptive  - 확장 가능한 커뮤니케이션.

- 서버나 클라이언트가 변경되더라도 오고가는 메시지는 언제나 self-descriptive 하므로 언제나 해셕이 가능합니다.
쉽게 말해서 : 링크는 동적으로 변경될 수 있습니다.








그럼 REST API 로 고쳐보자.

Self-descriptive 

방법 1 : Media Type 

Json - Content-Type: application/vnd.todos+json 

1. 미디어 타입을 하나 정의.
2. 미디어 타입 문서를 작성. 이 문서에 "id" 가 뭐고 "title" 이 뭔지 의미를 정의한다.
3. IANA에 미디어 타입을 등록한다. 이 떄 만든 문서를 미디어 타입의 명세를 등록한다.
4. 이제 이 메시지를 보는 사람은 명세를 찾아갈 수 있으므로 이 메시지의 의미를 온전히 해석할 수 있다.
   
-> SUCCESS 
단점 : 매 번 media Type 을 정의해야 합니다.




방법 2: Profile 

1. "id"가 뭐고 "title" 이 뭔지 의미를 정의한 명세를 작성합니다.
2. Link 헤더에 profile relation 으로 해당 명세를 링크합니다.
3. 이제 메시지를 보는 사람은 명세를 찾아갈 수 있으므로 이 문서의 의미를 온전히 해석할 수 있다.

-> SUCCESS 
단점 : 클라이언트가 Link 헤더(RFC 5988) 와 profile(RFC 6906)을 이해해야 한다.
    : Content negotiation 을 할 수 없다.




정리 

오늘날 대부분의 "REST API"는 사실 REST 를 따르지 않고 있다.
REST 의 제약조건 중에서 특히 Self-descriptive 와 HATEOAS 를 잘 만족하지 못한다.
REST 는 긴 시간에 걸쳐 (수십년) 진화하는 웹 애플리케이션을 위한 것이다.
REST를 따를 것인지는 API를 설계하는 이들이 스스로 판단하여 결정.
REST를 따르겠다면? Self-descriptive 와 HATEOAS 를 만족시켜야 한다.
    - Self-descriptive는 custom media type이나 profile link relation 등으로 만족시킬 수 있다.
    - HATEOAS 는 HTTP 헤더나 본문에 링크를 담아 만족시킬 수 있다.

REST 를 따르지 않겠다면, "REST 를 만족하지 않은 REST API"를 뭐라고 부를지 결정해야 할 것이다.
    - HTTP API 라고 부를 수도 있고,
    - 그냥 이대로 REST API 라고 부를 수도 있다.







