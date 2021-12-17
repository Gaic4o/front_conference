# ES6+ 비동기 프로그래밍과 실전 에러 핸들링

[ES6+ 비동기 프로그래밍과 실전 에러 핸들링][https://www.youtube.com/watch?v=o9JnT4sneAQ]




`이미지 가져오기`

``` javascript
const images = [
    {
        name: "HEART", url: ""
    },
       {
        name: "6", url: ""
    },
       {
        name: "하트", url: ""
    },
       {
        name: "도넛", url: ""
    },
]



const loadImg = url => new Promise(resolve => {
    let img = new Image();  
    img.src = url; 
    img.onload = function() {
        resolve(img); 
    }
    return img; 
}

loadImg(imgs[0].url).then(img => log(img.height)); 
```


`이미지들을 불러와서 모든 이미지의 높이를 더한다`

1. 이미지를 불러와 지는 시점에 height 을 구합니다. 

``` javascript
   img.onload = function() {
        resolve(img); 
    }
```

onload 일어나는 시점을 외부에 알려 전달 할 수 있어야 height 구할 시점을 알 수 있습니다. -> Promise 를 이용하면 즉시 해결 할 수 있습니다. 


``` javascript
async function f1() {
    const total = await imgs 
    .map(async ({url}) => {
        const img = await loadImg(url); 
        return img.height; 
    })
    .reduce(async (total, height) => await total + await height, 0); 

    log(total); 
}
```

정말 아슬아슬 하게 된 코드. 
만약 url 잘못 된 사용 하면 Error 가 발생합니다.

에러 문구를 추가 해 봅시다.
에러 라는 것은 발생이 되야 잡힙니다.

에러 자체가 발생이 안되는 상황.
에러를 발생시키는 방법.

-> e 이벤트가 오고 Promise 로 해서 try catch 사용 하기.


``` javascript
async function f1() {
    try { 
    const total = await imgs 
    .map(async ({url}) => {
        const img = await loadImg(url); 
        return img.height; 
    } catch(e) {
        log(e);
        throw e; 
    }
    })
    .reduce(async (total, height) => await total + await height, 0); 

    log(total); 
} catch (e) {
    log(0); 
}
}

```

이런 로직 비슷한 과정에서 이미지 로드가 아니라 post 날린 코드였다? 
가정 하면, 개발자는 에러 핸들링에 대해 post 날리는 코드 가정.

post 두 번 더 날라가지를 않기 코딩 했는데 post 가 날라가 버리고 데이터가 망가져 버림.
의도한 것과 다른 상황.

모르고 있을 수 있는 상황.
현재 코드는 아주 심각한 상황 입니다.

이 문제를 어떻게 해결 나가야 하는 가? 



`Promise, async/await, try/catch 잘 다루기`

``` javascript
function f2() {
    for (const a of imgs) {
        log(a.url); // 데이터를 보는 것 사용해야 하는 것이 url 이니까 
    }
}


// 딱 이 역할을 하는 것이 map 함수 입니다.
function* map(f, iter) {
    for (const a of iter) {
        // 3개가 출력 됩니다.
        log(a); // {name : url : ''..}, Promise <{pending}>, undefined 
        yield f(a); // 바깥에서 일으키는 부수효과를 내부 x 발생 o 직접 기입 x 함수에게 위임.  
    }
}


// 이제 f 함수와 a 인자를 어떻게 합성을 해 주어야 할까요? 
// 들어온 인자가 Promise 이면 a.then(f) 를 반환하고 아니라면 ? f(a) 를 반환합니다.
function* map(f, iter) {
    for (const a of iter) {
        yield a instanceof Promise ? a.then(f) : f(a); 
    }
}


function f2() {
    let acc = 0; 
    for (const a of map({url}) => url, imgs)) {
        log(a); 
        acc = acc + a; 
    }
    log(acc); 
}
f2(); 
```



reduce 를 이용해서 좀 더 안정적인 코드를 만들어 봅시다.


``` javascript
async function reduceAsync(f, acc, iter) {
    for await (const a of iter) {
        acc = f(acc, a);
    }
    return acc; 
}

async function f2() {
    log(
        await reduceAsync((a, b) => a + b, 0),
            map(img => img.length,
                map(({url}) => loadImage(url), imgs));
    )
}
f();
```

-> 이 코드에서도 Error 가 발생합니다.
Try catch 문을 사용하기.

에러가 났다고 출력 해 주기.
위에 쓴 코드보다는 훨씬 더 심플함.

많은 실제 사용 되는 서비스에서 몰래 몰래 에러가 발생합니다.
-> 데드락, 서버도 죽고 이런 원하지 않는 데이터가 쌓여서 정리하는 html 돌리고 그런 상황이 발생합니다.


``` javascript
async function f2() {
    try {
        log(
            await reduceAsync((a, b) => a + b, 0,
                map(img => img.height,
                    map({url}) => loadImg(url), imgs2));
        ) catch (e) {
            log('서버에 에러 전달', e);
            log(0);
        }
    }
}

```


또 이 코드가 좋은 코드는 아닙니다.
함수를 작성 할 떄 함수 안에서 부수 효과를 일으키는 것은 그렇게 좋지 않습니다.

즉 값을 리턴하는 형식으로 만들어 주는 것이 좋습니다.

``` javascript
async function f2() {
    try {
        log(
            return await reduceAsync((a, b) => a + b, 0,
                map(img => img.height,
                    map({url}) => loadImg(url), imgs));
        ) catch (e) {
            log('서버에 에러 전달', e);
            return 0;
        }
    }
}

f2().then(log);
```


이것보다 더 좋은 코드는 ?
-> 에러 핸들링을 아에 하지 않은 코드입니다.

일단 에러 핸들링을 하지 맙시다.
에러가 날 상황이면 에러가 발생합니다.

외부에 값을 직접 참조하는 것이 아니라 인자로 전달하는 것이 중요합니다.


``` javascript
const f2 = imgs => 
    reduceAsync((a, b) => a + b, 0,
        map(img => img.height, 
            map(({url}) => loadImage(url), imgs)));
```



문장이 아니라 표현식으로 바뀌어 에러 러지가 없습니다.
이 코드는 loadImage 한 다음 height 하고 숫자를 더하는 코드.

imgs -> 인자가 정확한 값 이면 에러가 날 수 없는 코드 입니다.
순수 함수 입니다.

이 함수 인지가 잘 못 되었을 떄 에러가 발생합니다.
잘못 됐을 떄 대비해서 에러 핸들링을 하거나, 타입이 뭔지 검사해서 체크를 하거나, 에러 핸들링을 하는 코드가 많은 데요. -> 좋은 코드가 아니라고 생각 합니다.

어떤 개발자는 절대 이미지가 잘못 된게 아닌 서비스가 있을 수 있고,
이런 것도 전달 될 수 있는 이미지가 있을 수 있습니다.

-> 하지만 f2 에서 에러 핸들링을 하는 것은 x 
바깥에서 하는 것이 중요합니다.

``` javascript
f2(imgs).catch( => 0).then(log);
f2(imgs2).catch( => 0).then(log); 
```

처음에 작성한 코드처럼 에러 핸들링을 작성하면 이 코드에서만 제약하는 것 입니다.
-> 에러를 중간에 잡아 로그를 찍고 log 를 찍지 않기를 원하는 개발자도 있을 수 있고.

에러가 터지는 개발자도 있습니다.



## 정리를 해 보자면.

- Promise, async await, try/catch 를 정확히 다루는 것이 중요.

- 제너레이터/이터레이터/이터러블을 잘 응용하면 코드 표현력을 더할 뿐 아니라 에러 핸들링도 더 잘할 수 있습니다.

- 순수 함수에서는 에러가 발생하도록 그냥 두는 것이 더 좋습니다.

- 에러 핸들링 코드는 부수효과를 일으킬 코드 주변에 작성하는 것이 좋습니다.

- 불필요하게 에러 핸들링을 미리 해두는 것은 에러를 숨길 뿐 입니다.

- 차라리 에러를 발생시키는게 낫습니다.
    sentry.io 같은 서비스를 이용하여
    발생하는 모든 에러를 볼 수 있도록 하는 것이 고객과 회사를 위해서 더 좋은 해법입니다.

    