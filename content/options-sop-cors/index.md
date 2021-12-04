---
title: "OPTIONS 요청은 처음 보는데요? [SOP, CORS]"
description: "서버 액세스 로그를 분석하던 도중 처음보는 메소드의 요청을 하나 발견합니다."
date: "2021-08-09T00:34:32.346Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/options-%EC%9A%94%EC%B2%AD%EC%9D%80-%EC%B2%98%EC%9D%8C-%EB%B3%B4%EB%8A%94%EB%8D%B0%EC%9A%94-sop-cors-398f780601bb
redirect_from:
  - /options-요청은-처음-보는데요-sop-cors-398f780601bb
---

Photo by [Jono Hirst](https://unsplash.com/@jonohirst?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

서버 액세스 로그를 분석하던 도중 처음보는 메소드의 요청을 하나 발견합니다.

undefined

OPTIONS? 뭔가하고 확인해봤더니 Request Body도 Response Body도 없습니다. 무엇을 위한 요청일까요?

이를 알아보기 전에 SOP라는 웹 브라우저 정책에 대해 살펴보고 넘어가야합니다.

### SOP : Same Origin Policy (동일 출처 정책)

undefined

-   MDN 문서에 따른 SOP의 정의는 다음과 같습니다.
-   SOP란 **한 origin에서 불러온 문서 / 스크립트가 다른 origin과 상호작용하는 것을 제한**하는 웹 브라우저 정책입니다.
-   다시 말하면, 같은 origin 으로만 요청을 주고 받을 수 있다는 뜻입니다.
-   조금 더 정확히 말하면, 다른 origin 으로의 read를 불허합니다.
-   그래서 자바 스크립트는 **자신이 실행된 문서**의 서버와 다른 서버에서 불러온 문서의 내용은 읽을 수 없습니다.
-   다만, 다른 origin 으로의 링크, 리다이렉트, form submit 등의 write 작업은 허용합니다.

#### origin(출처)의 정의

[https://developer.mozilla.org/en-US/docs/Learn/Common\_questions/What\_is\_a\_URL](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/What_is_a_URL)

-   어떤 두 URL의 **protocol(scheme), host(domain name), port**가 일치할 경우 둘은 같은 origin 입니다.
-   [http://store.company.com/dir/page.html](http://store.company.com/dir/page.html) 을 기준으로 같은 origin 인지 아닌지 비교한 표입니다.

undefined

### SOP의 의미?

-   SOP가 다른 origin에 대한 자원 혹은 그에 대한 접근을 제한하는 정책이라는 것은 알겠는데,
-   왜 그런 정책이 만들어 졌을까요?
-   SOP 를 무시하고 다른 origin 의 문서와 마구잡이로 상호작용이 가능하다면 보안상 큰 문제가 발생할 수 있습니다.
-   여기서 말하는 보안상의 문제란?
-   제가 [aaa.com](http://aaa.com/) 이라는 웹 사이트를 만들었다고 가정하겠습니다. 나름 실사용 유저도 많이 보유하고 성공한 웹사이트입니다.
-   하지만 나쁜 의도를 가진 사람이 [xyz.com](http://xyz.com/) 이라는 자신의 웹 서버로 유저를 유도하는 스크립트를 작성했습니다.
-   이 스크립트가 제 웹사이트에서 작동 가능해버리면 유저의 개인 정보는 [xyz.com](http://xyz.com/) 으로 다 넘어가 버리겠죠.
-   그래서 SOP 를 통해 다른 origin의 문서는 읽지 못하도록 막습니다.

### CORS : Cross-Origin Resource Sharing (교차 출처 리소스 공유)

-   하지만 SOP 에 따르면 [aaa.com](http://aaa.com/) 이라는 웹 사이트는 [bbb.com](http://bbb.com/) 이라는 제가 만든 api 서버와도 통신할 수 없습니다. origin 이 다르기 때문이죠.
-   그래서 SOP 를 우회하기 위한 여러가지 방법 중 **가장 안전하고 또 권장되는 방법**이 CORS 입니다.

undefined

-   MDN 문서에 따른 CORS의 정의는 다음과 같습니다.
-   CORS란 HTTP header에 추가적인 정보를 추가하여 서버가 브라우저에게 자기 자신뿐만 아니라 다른 origin 에서 요청한 정보도 허용할 수 있도록 알려주는 것을 말합니다.
-   만약 [aaa.com](http://aaa.com/) 이라는 웹사이트가 [bbb.com](http://bbb.com/) 이라는 api 서버의 자원을 사용하고 싶다면,
-   [bbb.co](http://bbb.co/)m 에 먼저 자신([aaa.com](http://aaa.com/))이 요청을 보내도 되는지 물어봐야합니다.
-   이 과정을 pre-flight 라 부릅니다.
-   [aaa.com](http://aaa.com/) 에서 [bbb.com](http://bbb.com/) 에 요청을 보낼 때 htttp header에 `{ Origin : aaa.com }` 으로 **요청하는 출처를 반드시 명시**해 주고
-   서버가 다시 응답을 보낼 때 http header 에 `{ Access-Control-Allow-Origin : 허용하는 주소 }` 로 **CORS 를 허용할 URI 을 명시**해 줍니다.
-   그러면 브라우저는 해당 http header를 해석하여 요청을 승인할지 거부할지 판단합니다.
-   브라우저가 승인하면 비로소 본래 하려던 요청을 서버에 보냅니다.
-   **위 pre-flight 절차에서 서버에게 요청할 때 사용하는 HTTP 메소드가 우리가 궁금했던 OPTIONS 메소드입니다.**

undefined

### OPTIONS

undefined

-   OPTIONS 메소드를 사용한 pre-flight 요청의 예시입니다.
-   클라이언트는 먼저 서버로 OPTIONS 요청에 Origin 헤더를 담아 보내고,
-   서버는 그에 대한 응답으로 `Access-Control-Allow-Origin` 헤더에 CORS 를 허용하는 URI 목록을 담아 보냅니다. (CORS 에 관여하는 부가적인 다른 헤더들도 존재합니다. e.g. `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers.`..)
-   해당 응답을 브라우저가 해석하여 CORS가 허용되었다 판단하면, 본래 보내려면 POST 요청을 서버로 보냅니다.
-   OPTIONS는 서버에서 추가 정보를 판별하는데 사용하는 HTTP/1.1 메서드이며,
-   safe 메서드이기 때문에 리소스를 변경하는데 사용할 수 없습니다.

#### 하지만 모든 요청마다 OPTIONS 요청을 하는 것은 아닙니다.

-   아래의 3가지 조건을 모두 만족하는 요청은 pre-flight 과정을 생략합니다.

1.  `GET`, `HEAD`, `POST` 메소드 중 하나
2.  user agent에 의해 자동으로 설정되는 (Connection, User-Agent, Fetch 스펙상 [forbidden header](https://fetch.spec.whatwg.org/#forbidden-header-name)로 정의되어 있는) 헤더외에 [CORS-safelisted request-header](https://fetch.spec.whatwg.org/#cors-safelisted-request-header)로 명시된 헤더들만 포함된 경우 (`Accept`, `Accept-Language`, `Content-Language`, `Content-Type` 등)
3.  Content-Type은 `application/x-www-form-urlencoded`, `multipart/form-data`, `text/plain` 중 하나

-   위 3가지 조건 중 하나라도 만족하지 않는 요청은 pre-flight 요청을 먼저 보냅니다.

---

Reference:

-   [Same-origin policy — Web security | MDN](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy)
-   [Cross-Origin Resource Sharing (CORS) — HTTP | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
-   [OPTIONS — HTTP | MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods/OPTIONS)
-   [\[TIL\]](https://nukeguys.github.io/dev/options-request/) `[OPTIONS](https://nukeguys.github.io/dev/options-request/)` [요청은 왜 발생하는가?](https://nukeguys.github.io/dev/options-request/)
-   [SOP(Same-origin policy) 란 무엇일까?](https://dongwooklee96.github.io/post/2021/03/23/sopsame-origin-policy-%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C/)
