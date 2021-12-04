---
title: "안드로이드 HTTP 라이브러리, Retrofit 분석"
description: "안드로이드 네트워크 통신 라이브러리의 발전 과정(HttpUrlConnection, OkHttp, Retrofit)과 Retrofit 라이브러리 분석"
date: "2021-01-19T07:10:28.797Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-http-%ED%86%B5%EC%8B%A0%EC%97%90-%EB%8C%80%ED%95%9C-%EA%B1%B0%EC%9D%98-%EB%AA%A8%EB%93%A0-%EA%B2%83-9c90f5625d3e
redirect_from:
  - /안드로이드-http-통신에-대한-거의-모든-것-9c90f5625d3e
---

undefined

### 안드로이드 네트워크 통신 라이브러리의 발전 과정

안드로이드에서 서버와 데이터를 주고받기 위해 HTTP 통신을 해야할 경우 다들 어떻게 처리하시나요?

초창기 안드로이드에서 네트워크 콜을 수행하기 위해선 HttpURLConnection 혹은 Apache HTTP Client 를 사용했습니다. 하지만 위 두가지 방법 모두 단점이 많았기에 Deprecated되고 OkHttp, Volley, Retrofit 등의 Third-Party 라이브러리가 생겨났죠.

요즘은 아마 Sqaure가 만든 라이브러리인 [Retrofit](https://square.github.io/retrofit/)을 많이 사용하는 것으로 알고있습니다. 하지만 그저 HTTP 통신은 Retrofit 라이브러리를 써! 라는 말만 듣고 무작정 사용하시는 분들이 많을겁니다.

왜 Retrofit을 써야하는지, Retrofit이 내부적으로 어떻게 구현되었는지, 그럼 Retrofit 이전에는 어떻게 HTTP 통신을 구현했는지를 간단한 코드와 함께 살펴보겠습니다.

---

### [HttpUrlConnection](https://developer.android.com/reference/java/net/HttpURLConnection.html)

undefined

http 통신을 위해 사용하는 가장 기본적인 클래스로 UrlConnection 클래스를 상속하며 abstract class입니다. URLConnection 클래스와 마찬가지로 생성자가 protected로 선언되어있기 때문에 기본적으로는 개발자가 직접 HttpURLConnection 객체를 생성할 수 없습니다. 보통 Url 객체의 openConnection()함수로 인스턴스를 리턴받아와서 사용합니다. setRequestMethod() 함수를 통해 request 형식을 변경할 수 있습니다.

HttpUrlConnection은 자바에서 기본적으로 제공하는 클래스이기 때문에 호환성 문제도 없고 가볍게 사용할 수 있다는 장점이 있지만 사용법이 복잡하고 간단한 api 하나를 부르는데도 보일러코드가 굉장히 길어집니다. 직접 비동기도 구현해야하는 수고로움은 덤이구요.

undefined

---

### [OkHttp](https://square.github.io/okhttp/)

undefined

OkHttp는 Square에서 개발한 HTTP 네트워크 통신을 위한 써드파티 라이브러리입니다. Okio를 기반으로 만들어졌으며, shared memory pool을 통해 기존 java I/O 라이브러리 보다 효율적인 입출력이 가능합니다. 그밖에도 HTTP/2가 지원되지 않을 때를 대비한 connection pooling, 다운로드 사이즈를 줄이기 위한 transparent GZIP, 반복되는 call을 효율적으로 처리하기 위한 response caching 등 개발자의 수고를 덜어줄 유용한 기능들을 제공합니다.

undefined

---

### [Retrofit](https://square.github.io/retrofit/)

undefined

이번 글의 주인공인 Retrofit 입니다. OkHttp와 동일하게 Square에서 만들어졌으며 OkHttp를 네트워크 레이어로 활용하여 그 위에 레이어를 한단계 더 추가해 만든 라이브러리입니다. OkHttp가 저수준의 네트워크 명령(request, response, caching)을 수행하고 Retrofit은 그 위에 abstraction layer를 얹어서 조금 더 사용하기 편하고, 간결하게 만든거죠.

그럼 Retrofit을 사용한 코드를 먼저 볼게요.

undefined

기본적으로 retrofit은 api query를 service interface를 이용하여 작성합니다.

그리고 api call을 하고자하는 url과 OkHttpClient를 이용하여 Retrofit Client의 인스턴스를 생성해줍니다. 이때 OKHttpClient 안에 Intercepter를 추가하여 http 통신 중간의 과정을 모두 log로 남길 수 있고 ConverterFactory를 추가하여 api response를 원하는 format으로 받을 수 있습니다.(JSON, Xml)

마지막으로 위에서 생성한 Retrofit Client 인스턴스로 service interface의 구현체를 생성하여 api call을 수행하는거죠.

undefined

Retrofit을 이용한 api 통신 과정은 위에서 설명한 바와 같습니다.

그러면 이제 코드를 한줄 한줄 살펴보겠습니다.

```
private final WeatherService service;

final Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("Some Url")
                .client(new OkHttpClient())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

service = retrofit.create(ApiService.class);
```

위 코드는 알아보기 어렵지 않을겁니다. Retrofit Client 인스턴스를 생서하는 코드죠. Builder 패턴을 사용했다는 것 말고는 특이한 점이 없습니다. OkHttpClient와 ConverterFactory를 입맛에 맞게 생성하여 추가해 주시면 됩니다.

```
service = retrofit.create(ApiService.class);
```

위 코드는 처음 봤을때 한번에 이해가 잘 되지 않습니다. 우리는 service interface만 정의했을 뿐인데 우리는 그 구현체를 가지고 api call을 수행하죠. retrofit이 뭔가 뒤에서 알아서 해준 덕분에 가능했던 것 같은데 무슨 일이 일어난 걸까요?

그 과정을 간략하게 나마 알아봅시다.

.create() 함수를 타고 들어가 보죠.

undefined

이 함수에 대한 대략적인 설명을 적어놓은 주석이 먼저 보이는데요. 뒤는 부가적인 설명이고 제일 중요한 문장은 첫 문장이 되겠네요.

`Create an implementation of the API endpoints defined by the service interface.`

“service interface에 정의된 API endpoints의 구현체를 만들어준다.” 라고 하네요.

API endpoint란 API가 서버의 리소스에 접근할 수 있도록 하는 주소(URL)를 말합니다. 그러므로 Retofit은 우리가 API endpoint에 대한 상세한 주소을 interface로 작성해 놓으면 .create() 함수를 통해 그 interface에 대한 구현체를 만들어 API에 요청을 보낼 수 있게 해주는 것이죠.

그렇다면 어떻게 우리는 interface만 작성하는데 Retrofit은 알아서 그 구현체를 만드는 걸까요?

create() 함수 내부를 살펴보겠습니다.

undefined

우선 해당 service가 valid 한지 validateServiceInterface() 함수를 통해 검사 합니다.(interface가 맞는지, invalid 하다면 IllegalArgumentException throw)

undefined

validateServiceInterface() 함수의 내부입니다.

valid 한지 검사하는 과정을 거친 후 if문 내부에서 Platform.get() 을 통해 platform type을 구한 후, service.getDeclaredMethods() 를 반복문으로 호출하게 되는데 이 함수를 통해 interface 내부의 함수들을 array 형태로 리턴하게 됩니다. 그리고 이 함수들을 loadServiceMethod() 를 통해 serviceMethodCache에 map형태로 저장합니다. (request type, method name, Annotations, arguments 등을 저장)

undefinedundefined

ServiceMethod 객체를 살펴봅시다.

undefinedundefined

위와 같이 ServiceMethod 객체는 해당 API endpoint에 대한(우리가 interface에 명시한 대로) 모든 정보를 담고 있습니다.

undefined

그리고 마침내 Method 객체에서 annotation과 parameter에 대한 정보를 가져오기 위한 java.lang.reflect package에 다다랐습니다. (getAnnotations(), getGenericParameterTypes(), getParameterAnnotations())

### Reflection?

Retrofit은 위처럼 곳곳에서 자바의 Reflection을 활용하고 있습니다. Reflection 이란 해당 클래스의 구체적인 type을 알지 못하더라도 해당 클래스의 method, type, variable 등에 접근할 수 있도록 해주는 자바 api 입니다.

컴파일 시간(compile time)에는 알 수 없는 클래스의 정보를 실행 시간(Run time)에 동적으로 분석 및 추출해낼 수 있는 것이죠.

이쯤에서 다시 create() 함수를 보겠습니다.

undefined

validateServiceInterface() 함수를 수행 한 이후의 코드입니다. 우리가 선언했던 service interface는 자바의 Proxy를 이용하여 실행 시간(run time)에 동적으로 객체가 생성됩니다.(Proxy는 Reflect 패키지에 속합니다.) 이 방식은 Proxy 중에서도 Dynamic Proxy(동적 프록시)라고 불립니다.

지정된 interface의 이름을 가지고 Reflection으로 객체를 생성한 후, 해당 객체의 메소드를 불러와 해당 메소드들이 불릴 때 마다 실행될 InvocationHandler의 invoke() 함수를 구현합니다.

프록시 객체로 method를 호출할 때 마다 invoke() 함수가 호출 되는데, 이때 method 객체와 원래 호출의 매개변수가 인수로 전달됩니다.

Proxy Pattern에 대해 잠시 설명 드리자면, Proxy는 단어가 의미하는 것처럼 대리의 역할을 하는 패턴을 의미합니다. 같은 인터페이스를 구현하고 있는 실제 요청을 처리할 객체와 이를 실행할 프록시 객체를 만들고, 프록시 객체가 실제 요청을 처리하는 객체를 갖고 있는 구조이죠.

undefined

아래 그림에서 Subject interface는 우리가 명세한 service interface이고 Retrofit은 Invocation Handler로 Proxy Pattern을 구현해준거죠.

undefined

---

### 이번 짧은 글에선 안드로이드에서 HTTP 통신을 위해 초창기부터 어떤 라이브러리들이 사용되었는지 살펴보고 최근 가장 많이 쓰이는 Retrofit에 대해서는 조금이나마 자세히 코드를 들여다 보고 나름대로 분석해 보았습니다.

### 아무렇지 않게 쓰던 Retrofit이지만 내부는 Reflection, Proxy 등 평소 잘 몰랐던 자바 기술과 함께 복잡하게 구현되어 있었습니다. 간단하게 살펴본거라 아직 완벽히 이해한 것은 절대 아니지만 그래도 내부의 흐름정도는 와닿는 것 같습니다.

### 어떤 라이브러리든 내부를 분석할 때 마다 느끼는거지만 저는 아직도 배워야 할 것이 너무나 많은것같습니다.

---

혹시 시간이 나신다면 이 [Podcast](https://fragmentedpodcast.com/episodes/46/)를 한번 들어보시는 걸 추천드립니다.

Jesse Wilson이라는 안드로이드 HTTP Client 개발자분이 진행한 팟캐스트인데 Apache HTTP client부터 OkHttp, Retrofit까지 안드로이드 HTTP Client의 전반적인 역사를 들려주십니다.

---

참고

[https://pluu.github.io/blog/android/2016/12/25/android-network/](https://pluu.github.io/blog/android/2016/12/25/android-network/)

[https://medium.com/@joycehong0524/android-studio-retrofit2-기본-사용법-retrofit-의문점-풀어헤치기-스압-f150db436add](https://medium.com/@joycehong0524/android-studio-retrofit2-%EA%B8%B0%EB%B3%B8-%EC%82%AC%EC%9A%A9%EB%B2%95-retrofit-%EC%9D%98%EB%AC%B8%EC%A0%90-%ED%92%80%EC%96%B4%ED%97%A4%EC%B9%98%EA%B8%B0-%EC%8A%A4%EC%95%95-f150db436add)

[https://medium.com/mindorks/understand-how-does-retrofit-work-c9e264131f4a](https://medium.com/mindorks/understand-how-does-retrofit-work-c9e264131f4a)

[https://blog.naver.com/cncn6666/221784973026](https://blog.naver.com/cncn6666/221784973026)

[https://mnworld.co.kr/1760](https://mnworld.co.kr/1760)
