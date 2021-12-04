---
title: '코틀린의 원시 타입'
date: 2021-12-03 21:21:13
category: 'kotlin'
draft: false
---
코틀린은 자바와 달리 원시 타입과 래퍼 타입을 구분하지 않는다. 이번에 그 이유와 코틀린의 내부에서 어떻게 원시 타입에 대한 래핑이 작동하는지 살펴보자.

## Int, Boolean

**자바는 원시 타입과 참조 타입을 구분한다.**

원시 타입(primitive type)의 변수에는 **그 값이 직접** 들어가지만, 참조 타입(reference type)의 변수에는 **메모리상의 객체 위치**가 들어간다.

원시 타입은 값을 더 효율적으로 저장하고 여기저기 전달할 수 있지만, 값에 대해 메소드를 호출하거나 Collection 에 원시 타입의 값을 넣을수가 없다. **(제네릭 클래스에 타입으로 원시 타입을 사용할 수 없다는 의미)** 그래서 자바는 원시 타입의 값을 한번 더 감싼 **래퍼 타입으로 원시 타입의 값을 참조 타입으로 바꿔서 사용**한다. 따라서 정수로 이루어진 Collection 을 정의하려면 `Collection<int>` 가 아니라 `Collection<Integer>` 를 사용해야한다.

하지만 코틀린은 자바와 다르게 원시 타입과 참조 타입(래퍼 타입)을 구분하지 않는다.

```kotlin
val i: Int = 1
val list: List<Int> = listOf(1, 2, 3)
```

참조 타입을 따로 구분하지 않으니 편리하긴 한데, 원시 타입과 참조 타입이 같다면 코틀린이 항상 그들을 객체로 표현한다는 걸까? 그러면 너무 비효율적이지 않을까? 다행히도 코틀린은 그러지 않는다.

코틀린은 **실행 시점에 숫자 타입은 가능한 한 가장 효율적인 방식으로 표현**된다.

코틀린의 Int 타입은 원시 타입인 int 로 수행가능할 경우 int 로 컴파일되고 원시 타입으로 수행이 불가능할 경우 (Collection 의 타입 파라미터로 넘기는 경우) Integer 객체로 컴파일 된다.

### Int?, Boolean?

하지만 자바의 null 참조는 참조 타입의 변수에만 대입할 수 있기 때문에 null 이 될 수 있는 코틀린의 타입은 자바의 원시 타입으로 표현할 수 없다. 그래서 Int? 처럼 null 이 될 수 있는 원시 타입을 사용하면 그 타입은 무조건 자바의 래퍼 타입으로 컴파일된다.

### 숫자 변환

코틀린은 한 타입의 숫자를 다른 타입의 숫자로 자동 변환해주지 않는다. 설령 결과 타입이 범위가 원래 타입의 범위보다 넓더라도 말이다.

코틀린은 모든 원시 타입에 대해(Boolean 제외) 변환 함수를 제공하니 이를 적극적으로 활용하자.

- 자바에서 new Integer(42).equals(new Long(42))는 false 이다.
- 두 박스 타입 간의 equals 메소드는 그 안에 들어있는 값이 아니라 박스 타입 객체를 비교하기 때문이다.

## Any, Any? : 코틀린의 최상위 타입

자바에서는 클래스 계층의 최상위에 Object 가 있다면 코틀린에는 Any 가 있다. 사용에 있어서 차이점이 있다면 코틀린은 null 이 될 수 있는 값과 없는 값을 구분하기 때문에 null 이 될 수 있는 변수는 Any? 를 사용해야 한다는 정도이다. 그래서 자바 메소드에서 Object 를 인자로 받거나 반환하면 코틀린에서는 Any!(플랫폼 타입) 으로 그 타입을 취급한다.

코틀린의 모든 클래스에는 toString, equals, hashCode 라는 세 가지 메소드가 들어있는데, 이 들은 모두 Any 클래스에 정의된 메소드를 상속한 것이다. 하지만 자바 클래스인 Object 에 정의된 wait 이나 notify 와 같은 메소드는 사용할 수 없는데, 이를 사용하고자 하는 경우 Object 타입으로 변수를 명시적으로 캐스팅 해야한다.

### Unit : 코틀린의 void

코틀린의 Unit 타입은 자바의 void와 같은 기능을 한다. 대부분의 경우 void와 Unit의 차이를 알기는 어렵다.

차이점이라면 void와는 다르게 Unit은 타입 인자로 쓸 수 있다. 이는 제네릭 타입을 반환하는 함수를 오버라이드 하면서 반환 값을 없애고 싶을 때 유용하다.

```kotlin
interface Processor<T> {
	fun process(): T
}

class NoResultProcessor : Processor<Unit> {
	override fun process() {
		// 로직
	} // -> return 을 명시할 필요가 없다.
}
```

인터페이스는 process 함수가 명확히 어떤 값을 반환하라고 요구하지만 타입을 Unit 으로 명시할 경우 우리가 명시적으로 Unit을 반환할 필요가 없다. 컴파일러가 자동으로 return Unit을 넣어주기 때문이다.

### Nothing 타입

코틀린에는 Nothing 이라는 신기한 타입이 하나 존재한다.

Nothing 은 '이 함수는 결코 성공적으로 값을 반환할 일이 없다' 는 뜻으로 쓰이는 타입이다. 가령 테스트 라이브러리들을 보면 fail 과 같은 함수가 존재하는데 이 함수는 특별한 예외를 던져서 현재 테스트를 실패시킨다. 이 fail 함수는 무조건 예외를 발생시키고 절대 성공적으로 값을 반환할 일이 없기 때문에 Nothing 타입을 사용한다.

```kotlin
fun fail(message: String) : Nothing {
	throw IllegaStateException(message)
}
```

Nothing 타입은 아무 값도 포함하지 않기 때문에 함수의 반환 타입으로만 쓸 수 있다. 위의 fail 함수는 다음과 같이 유용하게 사용할 수 있다.

```kotlin
fun address = company.address ?: fail("No address!")
```

위 코드에서 컴파일러는 company.address 가 null 인 경우 엘비스 연산자의 우항에서 예외가 발생한다는 사실을 파악하고 address의 값이 null 이 아님을 추론할 수 있다.

----

### Reference

#### [https://www.manning.com/books/kotlin-in-action](https://www.manning.com/books/kotlin-in-action)


