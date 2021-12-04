---
title: "[Kotlin] 무조건적인 data class 사용은 피하자."
description: "코틀린의 유용\b한 키워드 중 하나인 data class가 무엇인지 파악하고 사용시 유의할 점에 대해 간단히 알아보았습니다."
date: "2021-06-13T08:20:50.104Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/kotlin-%EB%AC%B4%EC%A1%B0%EA%B1%B4%EC%A0%81%EC%9D%B8-data-class-%EC%82%AC%EC%9A%A9%EC%9D%80-%ED%94%BC%ED%95%98%EC%9E%90-b33d16937b47
redirect_from:
  - /kotlin-무조건적인-data-class-사용은-피하자-b33d16937b47
---

undefined

[Data classes in Kotlin: how do they impact an application size](https://medium.com/bumble-tech/data-classes-in-kotlin-the-real-impact-of-using-it-6f1fdc909837)

### Data Class

Data Class는 어떤 기능을 제공하나?

#### destructuring declaration (componentN)

-   각 프로퍼티에 접근하는 componentN(1, 2, 3…) 메소드를 구현해준다.



#### copy()

-   객체를 복사하는 copy() 메소드를 제공한다.

#### toString()

#### equals() & hashcode()

코틀린의 data class는 위와 같이 개발자가 코드로 일일이 작성하기 번거로운 기능들을 자동으로 만들어 제공해준다.

### 하지만 data class 사용시 유의해야 할 점이 있다.

componentN 이나 copy() 의 경우 사용되지 않는다면 빌드 과정에서 optimizer(R8, ProGuard…)가 최적화 과정에서 삭제해준다.

하지만 toString(), equals(), hashcode() 는 사용유무에 관계없이 남는다.

**그래서 data class의 남용은 애플리케이션의 용량(size)를 증가시킬 수 있다.**

data class를 절대 사용하지 말라는 뜻은 아니다. 다만, data class를 사용할 지 말지 결정할 때 신중히 고려하자는 뜻이다.

-   equals()와 hashcode() 메소드가 필요한가? → 그렇다면 data class를 쓰는것이 맞다.
-   destructing 메소드가 필요한가? → 이거 하나만 보고 data class를 쓰는 것은 옳지않다.
-   toSring() 메소드가 필요한가? → toString() 메소드에 의존하는 비즈니스 로직은 없을 것으로 생각된다. toString() 메소드는 IDE의 힘을 빌리면 아주 간단하게 생성가능하니 이를 이용하자.
-   **레이어간의 데이터 전달을 위한 간단한 DTO를 만들기 위함** → **굳이 data class를 사용할 이유가 전혀 없다.** 기본적인 class를 사용하도록 하자.

### Returning two values from a function

data class의 destructuring declaration 기능 활용

물론 Kotlin Standard Library는 Pair나 Triple와 같은 클래스를 제공하니 아래처럼 따로 Result 클래스를 만들지 않아도 된다.


