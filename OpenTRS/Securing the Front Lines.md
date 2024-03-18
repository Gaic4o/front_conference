# Securing the Front Lines: Protecting Front-End Applications from Overlooked Vulnerabilities

[Securing the Front Lines: Protecting Front-End Applications from Overlooked Vulnerabilities](https://www.youtube.com/watch?v=IutA2l7ptcI)
발표자 : 김도현, Lead of Xint, Theori

발표를 듣고 얻어갈 수 있는 것들.

1. 실제 real world 취약점
2. exploit 난이도는 어렵지 않지만, `취약점이 크리티컬한 가성비 좋은 취약점`
3. Front-end 의 실수 `(어떻게 하면 더 안전하게 만들 수 있을지)`

## Front-end 보안의 중요성

과거 jQuery, PHP 등을 사용할 떄는 많은 취약점이 발생했습니다. 사용자가 입력을 직접 `SQL 쿼리`에 포함시키거나 웹 페이지에 그대로 출력하는 등의 위험한 관행이 흔했음.

### 오늘날에는 크게 개선됨.

- 보안 인식의 증가 : 현대의 개발자들은 코드 작성 시 보안을 중요한 부분으로 인식하게 됐습니다.
- 도구와 프레임워크의 발전 : Vscode 개발 도구 및, React, Vue 같은 프레임워크는 보안 취약점을 방지하도록 설계 됐습니다.

## Real world cases

Case #1: XSS

- framework 에 `방어가 강화`되어서, `XSS 취약점`을 발견하는 것이 어려워졌음.
- VS Code와 같은 `개발 에디터 도구`에서 `취약한 코드를 경고`로 표시해 줍니다. 코드 에디터에서 개발자가 보안 문제를 더 쉽게 인지하고, 수정할 수 있음

예로 - Vue.js 의 `v-html`, `dangerouslySetInnerHTML` 가 있음.

v-html 을 사용해서, XSS 을 만들 수 있습니다.

<script> 같은 악의적인 스크립트를 포함하면, v-html 디렉티브를 통해 이 script 가 페이지에 직접 렌더링되고 실행됩니다. 

```vue
<template>
  <div v-html="unsafeHtml"></div>
</template>

<script>
export default {
  data() {
    return {
      unsafeHtml: "<script>alert('XSS Attack!');</script>"
</script>
```

### Exposed Secret (노출된 비밀저 정보)

문제점 - 개발 시에 `API Key, Token, Password` 등 민감한 정보를 코드에 직접 포함하는 경우가 있습니다.
이러한 정보가 포함된 코드가 외부에 노출될 경우, 악의적 사용자가 이 정보를 이용해 시스템에 무단으로 접근하거나 데이터를 조작할 수 있습니다.

#### 예시

API 호출 시 하드코딩된 토큰 사용: API 호출 예에서 볼 수 있듯이, Authorization 헤더에 API 키를 직접 삽입하여 사용합니다. 이 코드가 공개된다면, 누구나 해당 API 키를 사용할 수 있게 됩니다.

```javascript
// fetch admin API
let uid = 31337;
fetch("https://admin-domain.com/api/admin/user/${uid}", {
  headers: {
    Authorization: "sk_live_AeC39HqLyjWDarjtT1zdp7dc",
  },
})
  .then((response) => response.json())
  .then((data) => setUsers(data))
  .catch((error) => console.error("Error fetching data:", error));
```

환경 변수 사용: `환경 변수를 사용하여 비밀 정보를 관리하는 것이 좋은 방법이지만`, 빌드 과정에서 이러한 변수가 실제 값으로 치환되어 번들 파일에 포함될 수 있습니다. 이 경우, 빌드 결과물을 통해 환경 변수의 값이 노출될 수 있습니다.

```javascript
const ref = useRef(null);
const SLACK_TOKEN = process.env.NEXT_PUBLIC_SLACK_TOKEN;
const CHANNEL_ID = process.env.NEXT_PUBLIC_CHANNEL_ID;

const SendMsg = async (event) => {
  console.log(event);
  console.log(ref.current.value || "Hello, World!");
  const url = "https://slack.com/api/chat.postMessage";
  const res = await axios.post(
    url,
    {
      channel: CHANNEL_ID,
      text: ref.current.value || "Hello, World!",
    },
    {
      headers: { authorization: `Bearer ${SLACK_TOKEN}` },
    }
  );
  console.log("Done", res.data);
};
```

### manifest

Next.js 사용할 떄, 빌드 과정에서 자동으로 생성되는 파일들 중 하나가 manifest 파일입니다.
`이 파일은 애플리케이션의 라우팅 구조, 사용된 자바스크립트 파일 등의 정보를 포함하고 있습니다.`
악의적 사용자가 이 파일을 분석 해, 애플리케이션의 구조를 파악하거나, 특정 버전의 라이브러리가 취약점을 가지고 있는 지 확인할 수 있습니다.

#### 예시

`_buildManifest.js` 이 파일은 애플리케이션의 페이지와 이들에 연관된 자바스크립트 파일의 매핑 정보를 포함합니다.
예를 들어, `/admin` 경로가 `manifest` 파일에 포함되어 있다면, 공격자는 해당 경로가 존재한다는 것을 알 수가 있고, 이를 타겟으로 삼을 수 있습니다.

```javascript
self.__BUILD_MANIFEST = function(s, c, a) {
  return {
    __rewrites: {
      beforeFiles: [],
      afterFiles: [],
      fallback: []
    },
    "/": [s, c, "static/chunks/pages/index-e4a1648c3ba5c83.js"],
    "/_error": ["static/chunks/pages/_error-bc1e98c0d4de1157.js"],
    "/about": [s, "static/chunks/pages/about-9dea9c18d1f5817d.js"],
    "/admin": [s, "static/chunks/pages/admin-94e80ab0ec1ab940.js"],
    "/dev": ["static/chunks/pages/dev-fa086b6e6225f009.js"],
    ...
  }
};
```

### Source map

Source Map 은 웹 개발 과정에서 생성된 `최종 코드(번들된 코드)`를 `원본 소스 코드`와 연결해 주는 파일.
웹 애플리케이션을 빌드하면서 코드를 `압축(minfiy)` 하거나 `번들(bundle)` 하게 되는데, 이 과정에서 원본 코드가 변형되어,
가독성이 떨어지고 디버깅이 어려워집니다.
`Source Map 을 사용하면, 개발자는 브라우저의 개발자 도구에서 변형된 코드 대신 원본 소스 코드를 볼 수 있게 되어, 디버깅이 용이해집니다.`

#### 문제점

Source Map 파일이 외부에 노출될 경우, `악의적인 사용자가 이를 이용해 애플리케이션의 구조를 파악하거나, 취약점을 찾아낼 수 있습니다`.
예를 들어, 로그인 로직이나, API Key, 주석에 적힌 민감한 정보 등이 담긴 원본 코드를 볼 수 있게 됩니다.

```javascript
const LoginPage = () => {
  const [username, setUsername] = useState("");
  const [password, setPassword] = useState("");

  const handleLogin = async (e) => {
    e.preventDefault();

    try {
      const loginResult = await login(username, password);
      console.log(loginResult);
      if (loginResult.result) {
        console.log('Login success:');
        localStorage.setItem('AUTH_TOKEN', loginResult.AUTH_TOKEN)
      } else {
        console.log('Login failed :(');
      }
    } catch (error) {
      console.error('Error during login:', error);
    }
  };

  return (
    ...
  );
};
```

```javascript
538636: function(e, t, n) {
  "use strict";
  n.r(t);
  var s = n(85893)
  , r = n(67294)
  , l = n(50819)
  , o = n(56935)
  , i = n(95376)
  , a = n(48139)
  , c = n(9008)
  , d = n.n(c);
  t.default = (0;
  let[e,t] = [0, r.useState("")],
      [n,c] = [0, r.useState("")],
      x = async t=>{
          t.preventDefault();
          try {
            let t = await (0, l.x4)(e, n);
            console.log(t);
            t.result ? (console.log("Login success:"), localStorage.setItem("AUTH_TOKEN", t.AUTH_TOKEN)) : console.error("Login failed :(")
          } catch (e) {
            console.error("Error during login:", e)
          }
      }
  ;
};
```

번들된 코드인 데 어떻게 읽겠어? 하지만 실제로 `Source map`을 보면, `minimize 안된 코드`를 볼 수 있습니다.
로그인이 안 된 상태면 바로 router. push -> Login page에서 가게 되어 있는 로직.
이러한 경로들이 코드에 포함되어 있다면, 서버 사이드 보안 검사 없이도 해당 경로에 접근을 시도할 수 있습니다.

```javascript
useEffect(() => {
  const isLoggedIn = checkLoginStatus();

  if (!isLoggedIn) {
    router.push('/login');
    return;
  }
}
```

번들링 process 주석은 일반적으로 제거가 되는데,
개발자가 번들러 설정을 잘못 구성한 경우나, 소스 맵이 제공되고 그것이 노출되면 주석을 볼 수 있게 됩니다.
-> 그리고 주석으로도 쉽게 Account, URL 등 다양한 것들을 알 수 있습니다.

```javascript
/*
   test Account: guest / guest1234
*/

// const API_HOST = "http://dev.api.example.com:8080"; // dev
// const API_HOST = "http://stage.api.example.com:8080"; // stage
const API_HOST = "https://api.example.com";

// TODO: Security Fix
// Bug-Fix: (2023-08-12) Cross-site scripting
```

### 대응 방안

- production 환경에 소스 맵 업로드하지 않기 : production 환경에서는 소스 맵을 업로드하지 않는 것이 좋습니다.
  아래 코드를 사용하여 webpack 에서 source map 을 사용하지 않게 할 수 있습니다.

```javascript
module.exports = { devtool: false };
```

- 접근 제어: 필요한 경우, Source Map 파일에 대해 인증이 필요한 서버에만 업로드하여 무단 접근을 방지합니다.
- 에러 로깅 도구 사용: 에러 로깅과 원본 코드 매핑을 도와주는 도구를 사용하여 개발자만이 에러 정보와 원본 코드를 확인할 수 있도록 합니다.
- 견고한 코드 작성: 코드가 실수로 노출되더라도 안전할 수 있도록, 보안을 고려하여 견고하게 작성합니다.
