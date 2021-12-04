---
title: "JVM의 Integer Optimization"
description: "JVM은 일정 범위의 Integer 객체를 캐싱해놓고 꺼내 쓴다."
date: "2021-08-11T05:05:08.511Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/jvm%EC%9D%98-integer-optimization-390c226f197a
redirect_from:
  - /jvm의-integer-optimization-390c226f197a
---

Photo by [Maksym Kaharlytskyi](https://unsplash.com/@qwitka?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

다음 코드와 실행 결과를 살펴보자.

undefinedundefined

똑같이 == 연산자로 비교했는데 1000을 할당한 `a == d` 는 false를, 100을 할당한 `b == c` 는 true를 반환한다.

어째서 이런 결과가 나온걸까?

### JVM의 Integer Optimization

JVM은 **\-128 ~ 127 사이의 Integer 객체를 캐싱**해놓고 필요할 때 마다 꺼내쓴다.

그리고 자바의 == 연산자는 referential equality 이기 때문에 주소값을 비교한다.

Integer b와 c는 JVM이 캐싱한 Integer 객체의 주소를 가리키기 때문에 동일한 곳을 바라볼 수 밖에 없다. 그래서`b == c` 는 true 이다.  
하지만 Integer a와 d는 각기 다른 주소의 Integer 객체를 가리키기 때문에 `a == d` 는 false 이다.

만약 a와 d의 값을 비교하고 싶다면 equals() 메소드를 사용해야한다.

[Integers caching in Java](https://stackoverflow.com/questions/3131136/integers-caching-in-java)

참고로 코틀린은 자바와 반대로

-   \== 가 structual equality를, (equals() 메소드)
-   \=== 가 referential equality 를 의미한다.

[Equality | Kotlin](https://kotlinlang.org/docs/equality.html)
