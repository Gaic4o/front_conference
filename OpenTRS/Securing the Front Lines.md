# Securing the Front Lines: Protecting Front-End Applications from Overlooked Vulnerabilities

[Securing the Front Lines: Protecting Front-End Applications from Overlooked Vulnerabilities](https://www.youtube.com/watch?v=IutA2l7ptcI)
발표자 : 김도현, Lead of Xint, Theori

발표를 듣고 얻어갈 수 있는 것들.

1. 실제 real world 취약점
2. exploit 난이도는 어렵지 않지만, 취약점이 크리티컬한 가성비 좋은 취약점.
3. Front-end 의 실수 (어떻게 하면 더 안전하게 만들 수 있을지)

왜 Front-end 보안에 대해 집중을 하게 됐나?
-> 이 부분에 잘 신경을 쓰지 못해서, effect 있는 취약점들이 많았음.

SPA 나오지 전까지, 과거 Jquery, PHP 등을 사용하였음.
-> 이떄는 Front-end 취약점이 많이 발생했음. (SQL Injection, XSS 등)
과거 jQuery, PHP 을 사용하던 시대에는 Front-end, Back-end 모두 취약점이 많이 발생했습니다. 
과거의 개발 환경과 언어들은 현재의 것들처럼 보안을 내장하고 있지 않았음 또는 과거에는 사용자 입력을 직접 SQL Query 포함시키거나, 웹 페이지에 그대로 출력하는 등의 위험한 관행이 흔했습니다. 

``` php
<?php
$username = $_POST['username'];
$password = $_POST['password'];

$sql = "SELECT * FROM users WHERE username = '$username' AND password = '$password'";
// 이후에 데이터베이스에 쿼리 실행
?>
``` 

오늘날에는 크게 개선됨.

- 보안에 대한 인식 증가 : 개발자들은 이제 보안을 코드 작성의 중요한 부분으로 인식.
- 현대적 개발 도구 : VS Code 와 같은 개발 에디터 도구에서 취약한 코드를 경고로 표시 해 줍니다. 코드 에디터에서 개발자가 보안 문제를 더 쉽게 인지하고, 수정할 수 잇음.
- 보안 기능이 강화된 프레임워크 : React, Vue 등은 현대 프레임워크는 기본적으로 많은 보안 문제를 방지하도록 설계 됐습니다. 

SPA(Single Page Application Vue, React) 이 등장하게 되면서, 생산성이 늘어나고, UX 표준이 많이 올라감.
-> Front-end 책임이 많이 올라가고, 비즈니스 영향을 줘서, 중요하게 됐습니다. 


## Real world cases 

Case #1: XSS 

- FrameWork 에 방어가 강화가 되어서, XSS 취약점을 발견하는 것이 어려워 졌음. 
- VS Code 와 같은 개발 에디터 도구에서 취약한 코드를 경고로 표시 해 줍니다. 코드 에디터에서 개발자가 보안 문제를 더 쉽게 인지하고, 수정할 수 잇음

``` javascript
dangerouslySetInnerHTML
```

그래도 Vue.js 에서는 크리티컬 하지 않지만, v-html 을 사용해서, XSS 을 만들 수 있습니다. 
v-html 이 뭘까? Vue 템플릿에 HTML 을 직접 렌더링하는 데 사용함. 
unsafeHtml 변수에서는 사용자로부터 입력 받을 수 잇는 데이터를 포함.
<script> 같은 악의적인 스크립트를 포함하면, v-html 디렉티브를 통해 이 script 가 페이지에 직접 렌더링되고 실행됩니다. 

``` vue
<template>
  <div v-html="unsafeHtml"></div>
</template>

<script>
export default {
  data() {
    return {
      // 사용자로부터 입력 받을 수 있는 HTML 콘텐츠
      unsafeHtml: "<script>alert('XSS Attack!');</script>"
    }
  }
}
</script>
```



Case #2: Exposed Secret 

- 민감한 정도, API Key, Token, Password 를 말합니다. 
사실 개발자들은 번들이 완료 된 코드를 잘 보지 않습니다. 

결제 API -> Authorization 값이 하드코딩 되어 있으면, 그대로 악용할 수 있음 (취소, 결제 등). 
복잡하게 Zero day 취약점을 발견하고, 복잡한 exploit 코드를 작성하는 것이 아닌 쉽게 발견 되서 악용할 수 있습니다. (bundle 시 그대로 주입이 됩니다.)

``` javascript
// fetch admin API
let uid = 31337;
fetch('https://admin-domain.com/api/admin/user/${uid}', {
  headers: {
    Authorization: "sk_live_AeC39HqLyjWDarjtT1zdp7dc"
  }
}).then((response) => response.json())
  .then((data) => setUsers(data))
  .catch((error) => console.error('Error fetching data:', error));
```

어떤 페이지에서 입력을 하게 되면, Slack 에 보내주는 Token
환경 변수를 통해 숨겨 졌다고 생각 해도, bundle 결과물인 bundle 리터럴 값으로 포함되어, 실제 환경변수의 값을 확인 해 볼 수 있습니다. 

``` javascript
const ref = useRef(null);
const SLACK_TOKEN = process.env.NEXT_PUBLIC_SLACK_TOKEN;
const CHANNEL_ID = process.env.NEXT_PUBLIC_CHANNEL_ID;

const SendMsg = async (event) => {
  console.log(event);
  console.log(ref.current.value || 'Hello, World!');
  const url = 'https://slack.com/api/chat.postMessage';
  const res = await axios.post(url, {
    channel: CHANNEL_ID,
    text: ref.current.value || 'Hello, World!'
  }, {
    headers: { authorization: `Bearer ${SLACK_TOKEN}` }
  });
  console.log('Done', res.data);
};
```


Case #3: manifest 

Next.js 에서 build 할 떄, 이런 파일이 발견됩니다. -> build 시 자동으로 생성되는 파일로, 빌드 process 결과물 중 하나 입니다. 
Next.js 에서 클라이언트 사이드에서 필요한 모든 자바스크립트 번들과 모듈의 매핑을 관리하기 위해 사용됩니다. 

아래 코드를 해커가 악용할 수 있음, 예로 들면 admin 이 있을 경우, 이 admin을 특정 목적으로 타켓하여 공격할 수 있고, 사이트의 구조를 파악하고, 어떤 자바스크립트 파일이 취약점을 가질 가능성이 있는 지 추측할 수 있습니다. 
아니면 _buildManifest.js 에서 서드파티 라이브러리의 버전을 식별하여, 취약점을 가진 라이브러리를 찾아 공격을 시도할 수 있습니다. 

_buildManifest.js 

``` javascript
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


Case #4: Source map 

Webpack: optimize, minimize code 

``` javascript
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

``` javascript
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

번들된 코드인 데 어떻게 읽겠어? 하지만 실제로 Source map 을 보면, minimize 안된 코드를 볼 수 있습니다. 
로그인이 안된 상태면 바로 router.push -> Login page 에서 가게 되어 있는 로직. 
이러한 경로들이 코드에 포함되어 있다면, 서버 사이드 보안 검사 없이도 해당 경로에 접근을 시도할 수 있습니다.

``` javascript
useEffect(() => {
  const isLoggedIn = checkLoginStatus();

  if (!isLoggedIn) {
    router.push('/login');
    return;
  }
}
```

번들링 process 주석은 일반적으로 제거가 되는데,
개발자가 번들러 설정을 잘못 구성한 경우나, 소스 맵이 제공되고 그것이 노출된 경우 주석을 볼 수 있게 됩니다. 
-> 그리고 주석으로도 쉽게 Account, url 등 다양한 것들을 알 수 있습니다. 

``` javascript
/*
   test Account: guest / guest1234
*/

// const API_HOST = "http://dev.api.example.com:8080"; // dev
// const API_HOST = "http://stage.api.example.com:8080"; // stage
const API_HOST = "https://api.example.com";

// TODO: Security Fix
// Bug-Fix: (2023-08-12) Cross-site scripting
```

-> source map 으로 취약점을 발견하지 않도록 최대한 노력을 할려면, 

1. production 환경에 소스 맵 업로드하지 않기 : production 환경에서는 소스 맵을 업로드하지 않는 것이 좋습니다. 
아래 코드를 사용하여 webpack 에서 source map 을 사용하지 않게 할 수 있습니다. 

``` javascript
module.exports = { devtool: false }
```

2. 소스 맵 파일은 이늦ㅇ이 필요한 곳에서만 업로드하기 : 소스 맵을 업로드해야 할 경우에는 인증 절차가 있는 서버에만 업로드하여, 무단 접근을 방지합니다. 
3. 에러 로그와 원본 소스 코드 매핑하기 위한 도구 사용: 에러 추적을 용이하게 하기 위해, 에러 로그와 원본 소스 코드를 매핑할 수 있는 도구를 사용합니다.
4. 코드가 노출되어도 괜찮을 정도로 견고하게 만들기: 코드를 작성할 때는 보안을 염두에 두고 견고하게 작성하여, 실수로 코드가 노출되더라도 문제가 되지 않도록 합니다.




