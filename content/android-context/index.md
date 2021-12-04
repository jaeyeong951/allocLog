---
title: "[android] Context란?"
description: "안드로이드 개발을 하다보면 자주 접하는 Context"
date: "2020-01-15T14:25:00.863Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/android-context%EB%9E%80-d5ddad300027
redirect_from:
  - /android-context란-d5ddad300027
---

undefined

안드로이드 개발을 하다보면 자주 접하는 Context

하지만 지금까지 이것의 정확한 역할이 무엇인지 모르면서 그냥 썼던것같습니다.

이번기회에 확실히 이해하고 넘어가야겠습니다.

먼저 Android Developer사이트의 설명부터 보겠습니다.

> _Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system._ **_It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc._**

Abstract class이며 실제 구현은 Android system이 담당하고 어플리케이션의 자원이나 클래스에 접근할 수 있게 한다고합니다.

항상 느끼는거지만 Developer사이트의 설명만 봐서는 한번에 와닿지가 않습니다.

Context라는 단어의 사전적 의미를 찾아보면 다음과 같습니다.

> **_con·text_** _1.(어떤 일의) 맥락, 전후 사정 2.(글의) 맥락, 문맥_

안드로이드의 context도 위의 뜻과 같습니다. 어플리케이션이나 객체의 현재 상태를 나타내주는 역할을 합니다. 방금 막 태어난 햇병아리같은 객체는 현재 자신이 위치한 환경(액티비티 / 어플리케이션)이 어떤 곳인지 대략적으로 알 필요가 있습니다. (“내가 지금 어디에 있고 난 다른 객체들과 어떻게 소통하지?”) 이럴때 context가 필요합니다.

예시를 하나 들어보겠습니다.

-   당신은 스타트업 SW 회사의 CEO입니다.
-   이 회사엔 DB, UI등 회사의 모든 일을 처리하는 수석 아키텍트가 있습니다.
-   이제 당신은 신입 개발자를 채용했습니다.
-   이때 그 신입개발자가 DB, UI중 어디에서 일할지, 어떤일을 할지 결정하는것은 수석 아키텍트입니다.

context의 역할은 다음과 같습니다.

-   어플리케이션의 resource 획득
-   새로운 activity 시작
-   view 생성
-   system service 획득

### Context는 Activity, Application, Service등의 base class입니다.

이와 관련하여 예시를 하나 더 들자면 context는 TV를 조종하는 리모컨이라고 생각할 수 있습니다. TV의 다양한 channel들은 어플리케이션의 자원들이겠죠. 리모컨은 TV의 다양한 channel들에 접근할 수 있게 해줍니다.

-   리모컨은 자원에 접근할 수 있게 해주고
-   이로써 당연히 리모컨을 가진 사람은 자원에 접근할 수 있습니다.

context를 얻는 방법으로는 `getApplicationContext()`, `getContext()`, `getBaseContext()` 혹은 `this`(Context를 확장한 class의 경우)등이 존재합니다.

```
//example
 TextView tv = new TextView(this);
```

참고)

[https://stackoverflow.com/questions/3572463/what-is-context-on-android#comment18833859\_3572553](https://stackoverflow.com/questions/3572463/what-is-context-on-android#comment18833859_3572553)[http://blog.naver.com/huewu/110085457720](http://blog.naver.com/huewu/110085457720)
