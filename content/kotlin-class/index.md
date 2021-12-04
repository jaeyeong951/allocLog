---
title: "[kotlin] class 사용법"
description: "평소 Java만 사용하다 kotlin을 사용하려 하니 헷갈리는 부분이 많아서 정리해두려고한다."
date: "2020-01-15T14:28:45.186Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/kotlin-class-%EC%82%AC%EC%9A%A9%EB%B2%95-24ee79062a96
redirect_from:
  - /kotlin-class-사용법-24ee79062a96
---

Kotlin의 class 의 특징과 사용법을 자바와 비교하며 간단히 설명합니다.

---

### open, final, abstract : 기본적으로 final

자바에서는 final 키워드를 사용해 명시적으로 상속을 금지하지 않는 이상 모든 클래스를 다른 클래스가 상속할 수 있다. 이는 편리해 보이지만 문제가 생기는 경우도 많다.

취약한 기반 클래스(fragile base class)라는 문제에 대해 들어본 적이 있는가? 하위 클래스가 기반 클래스에 대해 가졌던 가정이 기반 클래스의 변경으로 인해 깨져버린 경우에 발생하는 문제를 일컫는다.

어떤 클래스가 자신을 상속하는 방법에 대해 정확한 규칙(어떤 메소드를 어떻게 오버라이드해야 하는지 등)을 명세하지 않는다면 그 클래스의 클라이언트는 기반 클래스를 작성한 사람의 의도와 다른 방식으로 메소드를 오버라이드할 위험이 있다. 상속하는 모든 하위 클래스를 분석하는 것은 불가능하므로 기반 클래스를 변경하는 경우 하위 클래스의 동작이 예기치 않게 바뀔 수도 있다는 면에서 기반 클래스는 ‘취약’하다.

이 문제를 해결하기 위해 자바 프로그래밍 기법에 대한 책 중 가장 유명한 책인 조슈아 블로크의 Effective Java 에서는 “상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 상속을 금지하라”는 조언을 한다. 이는 곧 하위 클래스에서 오버라이드하도록 의도된 클래스나 메소드가 아니라면 모두 final로 만들라는 뜻이다.

코틀린은 이 철학을 그대로 따른다. 그래서 코틀린의 모든 클래스와 메소드는 기본적으로 final이다. 그래서 어떤 클래스의 상속을 허용하려면 클래스 앞에 open 변경자를 붙여야 한다. 더불어 오버라이드를 허용하고 싶은 메소드나 프로퍼티 앞에서 open 변경자를 붙여야만 한다.

```
open class RichButton : Clickable {
  fun disable() { /* ... */ } 
  // final 함수이므로 하위 클래스가 오버라이드할 수 없다.

  open fun animate() { /* ... */ } 
  // open 함수이므로 하위 클래스가 오버라이드할 수 있다.

  override fun click() { /* ... */ } 
  // 상위 클래스에서 선언된 open 함수를 오버라이드 한 것. 
  // 오버라이드한 메소드는 기본적으로 open이다. 오버라이드한 메소드의 구현을 하위 클래스에서 못하게 하려면 
  // final앞에 final을 명시해야한다.
}

open class RichButton : Clickable {
  final override fun click() { /* ... */ } 
  // 위 final은 쓸데 없는 중복이 아니다.
  // override한 메소드는 기본적으로 open이다.
}
```

---

### 주 생성자와 부 생성자

코틀린도 자바처럼 생성자를 하나 이상 선언할 수 있지만 자바와 다르게 주(primary) 생성자와 부(secondary) 생성자를 구분한다. 또한 코틀린은 초기화 블록(initializer block)을 통해 초기화 로직을 추가할 수 있다.

### 주 생성자와 초기화 블록

```
class User(val nickname: String)

class User constructor (_nickname: String) {
	val nickname: String
	init {
		nickname = _nickname
	}
}

class User(_nickname: String) {
	val nickname = _nickname
}
```

위의 세가지 클래스 선언은 완전히 동일한 의미이다. 맨 위의 간결한 표현을 풀어서 쓰면 아래 두 표현과 같다.

클래스 이름 바로 뒤의 괄호로 둘러싸인 코드를 주 생성자라고 부른다.

주 생성자는 생성자 파라미터를 생성하고 그 생성자 파라미터에 의해 초기화되는 프로퍼티를 정의하는 두 가지 역할을 한다.

init 키워드로 시작하는 초기화 블록에는 클래스의 객체가 만들어질 때(인스턴스화될 때) 실행될 초기화 코드가 들어간다. 코틀린은 주 생성자에 어떠한 코드도 들어갈 수 없기에 만들어진 블록이다. 필요에 따라 여러 초기화 블록을 선언하는 것도 가능하다.

클래스의 본문에서 val 키워드로 프로퍼티를 정의하고 주 생성자의 파라미터로 초기화하는 것도 가능하지만, 생성자 파라미터의 앞에 val을 추가하는 방식으로 프로퍼티의 정의와 초기화를 한번에 수행할 수 있다.

```
class User(val nickname: String) -> "val"은 이 파라미터에 상응하는 프로퍼티를 생성
```

함수 파라미터와 마찬가지로 클래스의 생성자 파라미터에도 디폴트 값을 정의할 수 있다.

```
class User(val nickname: String, val isSubscribed: Boolean = true)
```

클래스의 인스턴스를 만들기 위해 new 키워드를 쓸 필요없다. 그저 생성자를 호출하기만 하면 된다.

어떤 클래스를 상속하는 자식 클래스를 정의하려면 주 생성자에서 기반 클래스의 생성자를 호출해야한다.

```
open class User(val nickname: String) { ... }
class TwitterUser(nickname: String) : User(nickname) { ... } 
-> 부모 클래스인 User의 생성자 호출
```

이 규칙으로 인해 기반 클래스의 이름 뒤에는 꼭 빈 괄호가 들어가거나 인자가 있는 괄호가 들어간다.

반면 인터페이스는 생성자가 없기 때문에 어떤 클래스가 인터페이스를 구현하는 경우 아무 괄호도 붙지 않는다.

그래서 클래스의 정의를 볼 때 기반 클래스 이름 뒤에 괄호가 있는 없는지를 보면 쉽게 기반 클래스와 인터페이스를 구분할 수 있다. → 자바처럼 implements, extends 로 구분하지 않고 “:” 로 통일한 이유

### Private Constructor

어떤 클래스를 클래스 외부에서 인스턴스화하지 못하게 막고 싶다면 모든 생성자를 private으로 만들면 된다. 다음과 같이 주 생성자에 private 변경자를 붙일 수 있다.

```
class Secretive private constructor() {}
// 클래스의 유일한 주 생성자가 private
```

Secretive 클래스는 주 생성자밖에 없고 주 생성자는 private이다. 그래서 외부에서 Secretive 클래스를 인스턴스화할 수 없다. companion object 안에서 이런 private 생성자를 호출하면 좋다.

자바의 경우 유틸리티 함수들을 담아두기 위해 인스턴스화 할 필요가 없는 정적 유틸리티 클래스를 정의해야했고, 싱글턴 클래스는 미리 정한 팩토리 메소드 등의 생성 방법을 통해서만 객체를 생성해야 했다. 이 경우 자바는 private 생성자를 정의해서 클래스를 다른 곳에서는 인스턴스화하지 못하게 막았다. 코틀린은 이런 경우를 언어에서 기본적으로 지원한다. 정적 유틸리티 함수를 담는 클래스는 필요없다. 최상위 함수를 사용하면 된다. 싱글턴 클래스를 만들고 싶으면 object를 선언하면 된다.

### 부 생성자

코틀린은 생성자를 여러개 생성하는 경우가 자바보다 훨씬 적다. 디폴트 파라미터를 이용하면 굳이 생성자를 여러개 만들 필요가 없기 때문이다.

주 생성자는 constructor 키워드를 생략할 수 있지만 부 생성자는 그럴수 없다.

부 생성자의 중요한 특징으로는 주 생성자가 존재할 경우 부 생성자는 반드시 주 생성자에게 생성자를 위임해야한다.

```
class Person(val name: String) {

    var age: Int = 20
    var height: Int = 500

//    컴파일 에러
//    constructor(name: String, age: Int) {
//        this.age = age
//    }

    constructor(name: String, age: Int) : this(name) {
        this.age = age
    }

    constructor(name: String, age: Int, height: Int) : this(name, age) {
        this.height = height
    }
}
```
