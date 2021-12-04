---
title: "Android DI with Hilt - Hilt를 이용한 의존성 주입"
description: "google이 만든 안드로이드의 강력한 DI 라이브러리"
date: "2020-08-05T07:03:34.031Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/android-di-with-hilt-hilt%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85-5076d9e9c46b
redirect_from:
  - /android-di-with-hilt-hilt를-이용한-의존성-주입-5076d9e9c46b
---

#### android development

undefined

### DI란?

-   Dependency Injection (의존성 주입)
-   두 객체 사이에 의존성이 존재할 때, 객체의 생성을 의존하는 객체에서 직접 하는 게 아니라 **외부에서 생성**한 후 **주입** 시켜주는 것

```
class A(private val b : B) {
    fun run() {
        b.run()
    }
}
```

-   보통은 위와 같이 생성자로 주입하는 방식을 많이 사용한다.
-   의존 받는 객체(A)는 의존 객체(B)의 구현을 알 필요가 없다. → 프로그램의 설계가 훨씬 유연해진다.
-   의존 객체의 생성은 프레임워크나 라이브러리가 담당하는데 안드로이드의 경우 Koin, Dagger, Hilt와 같은 라이브러리를 이용한다.
-   위의 예시에서는 간단히 클래스 A, B를 들었으나, 안드로이드에서는 주로 MVVM 아키텍처 패턴 사용시 repository와 viewModel의 주입에 DI를 사용한다.
-   그렇다고 DI가 유일한 혹은 완전한 해답이라고 단정지어서는 안되며 소프트웨어 개발에 있어서 유연한 설계를 위해 주로 쓰이는 패턴정도로 보면 될 것 같습니다.

---

### Hilt

Hilt는 Square에서 만든 Dagger기반의 DI 라이브러리로 Annotation을 이용한 컴파일 타임 generated code로 의존성 주입을 구현하였습니다.

기존의 Dagger는 오류를 컴파일 타임에 검증이 가능하고 퍼포먼스가 준수하다는 특징은 장점이었지만 과도하게 많은 annotation과 보일러 플레이트 코드 때문에 학습 비용이 높다는 단점이 분명했습니다.

Hilt는 Dagger의 강력한 기능은 그대로 활용하면서 불필요한 annotation을 제거하여 사용하기 훨씬 쉽게 만든 라이브러리입니다.

---

### Hilt 시작하기

의존성을 주입할 시작점을 정합니다.

-   `@HiltAndroidApp`, `@AndroidEntryPoint`

undefinedundefined

-   위처럼 해당 Application, Activity 혹은 Fragment에서 의존성 주입을 할 것이라고 명시합니다.

Constructor로 의존성을 주입 받는 포인트를 선언합니다.

-   `@Inject`

의존성 호출 포인트를 선언합니다.

-   `@Inject constructor`

의존성을 생성할 Module을 정의합니다.

-   `@Module`, `@Provides`, `@Binds`

-   ActivityComponent::class ??

undefined

-   안드로이드의 프레임워크 클래스들(Activity, Application)은 각각 생명주기에 맞는 Component를 가지고 있는데 그 Component가 Module로 의존성을 생성하고 @Inject로 요청한 변수에 의존성을 주입하는 것
-   Provides와 Binds는 형태만 다를 뿐 기본적으로 interface와 그 구현체를 묶어주는 역할을 합니다.

위처럼 Hilt는 Dagger와 달리 annotation의 개수가 적을 뿐더러 훨씬 직관적입니다.

그리고 Hilt는 AAC(Android Architecture Component) **ViewModel**의 주입 또한 지원합니다.

-   `@Assisted` annotation을 사용하여 SavedStateHandle도 주입이 가능합니다.

---

### Hilt로 Test하기

Hilt는 Unit Test는 지원하지 않습니다.

Mocking으로 fake repository같은 의존성 생성이 가능합니다.

통합 Test에서 Hilt로 의존성 생성이 가능합니다.

-   `@HiltAndroidTest`, `@HiltAndroidRule`

테스트 클래스 단위로 Module을 재선언 할 수도 있습니다.

-   `@UninstallModules`를 활용하여 필요한 모듈을 재선언합니다.

프로덕션 코드에 영향을 주지 않고 개별 테스트 DI가 가능합니다.

---

### Hilt 조금 더 자세히 살펴보기

Hilt는 조금의 annotation으로 DI를 손쉽게 구현할 수 있지만 너무 abstract해서 DI가 대체 어떻게 이루어지는지 파악하기가 힘듭니다.

그래서 build generated code를 보며 hilt의 구현을 조금이나마 이해해 보도록 하겠습니다.

당장 viewModel의 경우 repository를 inject받아야 하는데 코드상으로는 constructor에 선언해주는게 전부입니다. annotation조차 없죠. 하지만 generated code를 보면 내부적으로 @Inject를 통해 repository를 주입 받습니다.

undefined

그리고 module들은 application단의 component가 가지고 있으며

undefined

ViewModel의 factory에 의존성을 주입합니다.

undefined

액티비티나 프래그먼트에서 by viewModels()를 호출시 일어나는 과정을 간단히 살펴보면 다음과 같습니다.

undefinedundefinedundefined

`by viewModels()`는 기본적으로 2개의 인자를 받을 수 있습니다.

factoryProducer로 custom factoryProducer를 등록할 수 있고 ViewModelStoreOwner도 새롭게 정의하여 등록할 수 있습니다.

따로 선언하지 않을 시 ViewModelStoreOwner는 해당 프래그먼트나 액티비티겠지만 `findNavController().getViewModelStoreOwner(navGraphId)`와 같이 subGraph단위로 더 넓게 설정하여 sharedViewModel도 구현할 수 있을것으로 보입니다.

`createViewModelLazy()`는 ViewModelLazy를 생성하기 위한 Helper Method입니다. factoryProducer가 주어지지 않을 시(=null) default factory를 넘겨줍니다.

그리고 `ViewModelProvider`에서 해당 store와 factory를 이용해 viewModel을 생성하거나 cache된 viewModel을 불러옵니다.

사실 이 내용은 Hilt보다는 viewModel 그 자체에 관련된 내용이라 이해하지 않고 넘어가셔도 사용하는데 지장은 없습니다.

---

안드로이드 DI 라이브러리인 Hilt에 관해 아주 간단히 적어보았습니다. Dagger의 강력한 기능을 그대로 가져오고 복잡한 annotation은 제거하여 비교적 쓰기 쉽게 만들어졌습니다. 그래도 annotation 기반이다 보니 다소 추상적이라 구조가 확 와 닿지는 않습니다.

다른 DI 라이브러리 중 하나인 Koin과 비교하면 에러가 런타임에 잡히는 Koin과 달리 Hilt는 Dagger기반이기에 대부분의 에러가 컴파일 타임에 색출이 됩니다.

사용하기는 Koin이 더 편하고 직관적이라는 평이 많으나 아무래도 더 안정적인건 Hilt라고 봐야할 것 같습니다. 런타임 에러를 인지하지 못하고 제품을 릴리즈할 수도 있기 때문입니다.
