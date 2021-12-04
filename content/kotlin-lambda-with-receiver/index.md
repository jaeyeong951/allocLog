---
title: "[Kotlin] Lambda with receiver"
description: "Lambda with receiver : 수신 객체 지정 람다"
date: "2021-08-26T15:16:20.777Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/kotlin-lambda-with-receiver-5c2cccd8265a
redirect_from:
  - /kotlin-lambda-with-receiver-5c2cccd8265a
---

undefined

### Lambda with receiver : 수신 객체 지정 람다

코틀린의 조금은 특이한 람다와 Receiver 라는 개념에 대해 알아보자.

### Receiver 란?

코틀린의 Receiver 를 내 나름대로 간단히 정의하자면

> _객체 외부의 람다 코드 블록을 마치 해당 객체 내부에서 사용하는 것 처럼 작성할 수 있게 해주는 장치_

이다.

저 한문장만 보면 무슨 의미인지 와닿지 않으니 천천히 아래 설명을 보자.

```
block : T.() -> R
```

위 람다 블록은 객체 T를 receiver로 이용하여 객체 R을 반환한다.

이 때 객체 T를 **receiver** 라 부르고 위와 같이 receiver를 사용하는 람다를 **lambda with receiver** 라 부른다.

```
block : (T) -> R
```

위의 경우 객체 T를 리시버가 아니라 람다 파라미터로 받는다.

### apply 와 also : 리시버와 파라미터의 차이

람다에서 객체를 리시버로 받는 것과 파라미터로 받는 것에는 어떤 차이가 있을까? 이를 알아보기 적합한 함수 두개가 있다. 코틀린의 scope function 중 일부인 apply와 also를 살펴보자.

```
public inline fun <T> T.also(block: (T) -> Unit): T {
    block(this)
    return this
}

public inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}
```

also 는 객체를 람다 파라미터로 받고 apply는 객체를 리시버로 받는다.

```
class person {
    var name = "kotlin"
	
    private val id = "1541"
}

person.also {
    println("my name is ${it.name}")
}

person.apply {
    println("my name is $name")
}
```

apply 와 also 를 사용하는 간단한 예제 코드이다. 차이가 바로 느껴질텐데, also 를 사용한 람다 블록에서는 해당 객체에 접근할 때 it 을 사용하고 apply 는 this 를 사용한다. this 는 생략가능하므로 위 코드에서는 생략했다.

처음에 이런 코드를 보고 이런 생각이 먼저 들었다. “it이랑 this를 사용했다 차이밖에 없잖아? this를 사용한다고 해서 객체 내부의 private 변수를 사용할 수 있는것도 아닌데…” 맞는 말이다. 둘의 차이는 it과 this 뿐이며 this를 사용한다고 해서 객체 내부의 private 변수에 접근이 가능 하다던가 하는 부가적인 기능은 없다.

### it vs this

**하지만 it과 this는 단순히 키워드의 차이가 아니다. 그 이상이다.**

also 는 객체를 람다 아규먼트로 받기 때문에 객체에 접근할 때 it(혹은 내가 정의한 다른 이름)을 사용하며, 이는 코드가 **객체 외부에서 해당 객체에 접근한다는 인상**을 강하게 준다.

이에 반해 apply 는 객체를 람다 리시버로 받기 때문에 객체에 접근할 때 this(혹은 생략)을 사용하며, 코드가 해당 **객체의 외부가 아니라 객체 내부에 있는듯한 인상**을 준다.

이 차이 때문에 also 와 apply 는 그 쓰임새가 완전히 달라진다. 내가 작성하고자 하는 코드의 semactics 에 따라 also를 쓸지 apply를 쓸지 결정하는 것이다.

apply 는 방금 말했듯이 코드 블록이 객체 내부에 있는 듯한 느낌을 주기 때문에 주로 객체를 초기화 하는 코드 혹은 객체의 상태를 변경하는 코드에 많이 사용된다.

```
person.apply {
    name = "steven"
    age = 21
}
```

also 는 이와 달리 객체를 외부에서 접근하는 느낌을 주기 때문에 해당 객체와 더불어(혹은 이용해서) 어떠한 행위를 수행하고자 할 때 쓰인다.

```
person.also {
    println("my name is ${it.steven}")
}
```

정말 말 그대로 apply와 also라는 단어 그 본연의 의미에 맞게 쓰는 것이다.

매번 느끼는 거지만 메소드 네이밍 정말 탁월한 것 같다. 코틀린 language designer 분들 대단하다.

apply 로 구현할 수 있는 기능은 also 로도 구현할 수 있고 그 반대도 마찬가지다. 이말은 즉슨, 두 함수는 기능적인 면에서는 동일하다고 볼 수 있다는 뜻이다. 하지만 둘이 각자 apply와 also라는 자신에게 꼭 맞는 이름을 가지고 각자에게 맞는 장소에 쓰일 수 있는 이유는 둘이 가지는 \*\*의미(semantics)\*\*가 다르기 때문이다.

각 함수의 의미와 그 차이를 제대로 이해하지 않고 오직 기능적인 면과 문법적인 면만 바라보고 작성한 코드는 코틀린이라는 언어가 의도한 바가 아닐 것이며 가독성을 해치는 코드이기에 좋은 코드라고 보기도 어려울 것이다.

---

Receiver 라는 개념이 코틀린에서 아주 대단한 개념은 아니지만 람다와 확장함수에서 사용되어 우리의 코드를 한층 더 풍요롭게 해준다. 코틀린 디자이너들이 리시버를 설계함에 있어서 의도했던 바를 이해하여 이 간결하고 쉬운 문법으로 조금 더 깊은 의미를 코드에 담아보자.

---

[What is a “receiver”?](https://discuss.kotlinlang.org/t/what-is-a-receiver/18588/9)

[Scope functions | Kotlin](https://kotlinlang.org/docs/scope-functions.html)

[https://kotlinlang.org/docs/lambdas.html#function-literals-with-receiver](https://kotlinlang.org/docs/lambdas.html#function-literals-with-receiver)

[코틀린 입문 스터디 (16) Lambda with Receiver](https://medium.com/@mook2_y2/%EC%BD%94%ED%8B%80%EB%A6%B0-%EC%9E%85%EB%AC%B8-%EC%8A%A4%ED%84%B0%EB%94%94-16-lambda-with-receiver-e265044206f9)
