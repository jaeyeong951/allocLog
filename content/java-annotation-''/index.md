---
title: "Java Annotation : ‘주석’?"
description: "annotation를 우리말로 번역하면 ‘주석’이다. 주석은 어떤 단어나 문장을 해당 페이지 밖에 조금 더 쉽고 친절하게 풀어서 설명해놓은 것을 뜻하는데, 자바에서 annotation도 이 단어 본래의 뜻과 크게 다르지 않다."
date: "2021-11-27T17:16:38.843Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/java-annotation-%EC%A3%BC%EC%84%9D-eaee19e1333d
redirect_from:
  - /java-annotation-주석-eaee19e1333d
---

Photo by [Kevin Ram](https://unsplash.com/@kevin_ram26?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

### **Annotation?**

annotation를 우리말로 번역하면 ‘주석’이다. 주석은 어떤 단어나 문장을 해당 페이지 밖에 조금 더 쉽고 친절하게 풀어서 설명해놓은 것을 뜻하는데, 자바에서 annotation도 이 단어 본래의 뜻과 크게 다르지 않다. 아니 오히려 정확히 이를 따르고 있다. 왜 그런지 알아보자.

자바에서 annotation은 우리가 작성하는 코드와는 별개로 컴파일러 혹은 런타임 중 애플리케이션에 특별한 정보(메타데이터)를 제공하는 역할을 한다. 컴파일러는 우리가 곳곳에 집어넣은 annotation을 보고 해당 위치에 코드를 삽입하거나(AOP) 런타임에 특정 동작을 수행(Reflection)하도록 할 수도 있다. 때문에 이를 적절하게 잘 활용하면 소스코드의 가독성과 구조를 크게 향상시킬 수 있다.

우리가 책을 읽으며 흔해 봐왔던 주석들을 생각해보자. 보통 단어 뒤에 작게 숫자 혹은 기호(\*)로 꼬리처럼 달아 놓고 페이지 바깥 맨 밑 귀퉁이에 이와 연결지은 설명글(혹은 작가나 변역가의 사족)이 있을 것이다. 페이지( = 우리가 작성중인 코드, 비즈니스 로직) 바깥에 달아놓은 설명글( = 컴파일러가 수행하는 코드)이다. 자바의 annotation이 동작하는 방식과 너무나 유사해 보이지 않나.

### **Java Annotation**

자바에서 annotation은 한줄로 정의하면 메타데이터를 삽입하는 것이지만 실질적인 용도를 적어보면 다음과 같다.

-   컴파일러에게 문법 오류를 캐치할 수 있도록 정보를 제공
-   애플리케이션이 런타임에서 특정 동작을 수행하도록 정보를 제공
-   컴파일러에게 빌드 시 코드를 자동으로 생성하도록 정보를 제공

어노테이션은 자바가 기본적으로 제공하거나(@Override, @Deprecated), 스프링이 제공하는 것(@Controller, @Service, @Component)외에도 우리가 직접 커스텀하여 제작할 수 있다.

### **Spring에서 자주 쓰이는 Annotation**

#### **@Configuration**

-   스프링 컨테이너에게 해당 클래스가 1개 이상의 Bean 객체를 구성하는 클래스임을 알려준다.

#### **@Bean**

-   개발자가 직접 제어가 불가능한 외부 라이브러리를 이용해서 만든 결과 인스턴스를 스프링 빈으로 등록하려 할때 사용
-   개발자가 정의한 메소드를 통해 반환되는 객체를 스프링 빈으로 등록하는 것

#### **@Component**

-   개발자가 직접 작성한 클래스를 스프링 빈으로 등록하고자 하는 경우 사용
-   해당 객체를 사용하기 원하는 클래스에서 주입하여 사용
-   이 클래스를 내가 정의했으니 스프링 빈 객체로 등록해 달라

#### **@Repository**

-   DAO에 특화된 어노테이션
-   Repository 클래스에 주로 사용
-   리포지토리 클래스들을 @Component 어노테이션을 사용하여 스프링 빈으로 등록해도 상관 없지만
-   @Repository 어노테이션은 @Component 어노테이션이 가진 기능과 함께 DAO를 담당하는 클래스에서 발생할 수 있는 unchecked exception들을 스프링의 DataAccessException으로 처리할 수 있는 기능이 있다.

#### **@Service**

-   Service 레이어를 담당하는 클래스에 주로 사용
-   @Component 어노테이션을 사용해도 무관
-   특별한 기능은 없지만 해당 클래스가 Service 레이어임을 명확하게 한다.

#### **@Controller**

-   Controller를 담당하는 클래스에 사용
-   해당 클래스에서 @RequestMapping 관련한 어노테이션을 사용할 수 있다.

#### **@ResponseBody**

-   View가 아니라 JSON 형식의 값을 응답할 때 사용한다.
-   문자열을 리턴하면 그 값을 http response header가 아닌 response body에 넣는다.
-   객체를 반환한다면 Jackson 라이브러리를 이용하여 자동으로 문자열로 변환된다.
-   Context에 설정된 viewResolver를 무시하는 것

#### **@RestController**

-   @Controller + @ResponseBody

undefined

-   API 기능 지원에 특화된 어노테이션

#### **@Request(POST, GET…)Mapping**

-   요청 URL을 어떤 메서드가 처리할 지 명시한다. → Mapping
-   컨트롤러 클래스에 선언된 메서드에 사용

#### **@ComponentScan**

-   하위 패키지의 객체들을 스캔하면서 @Component 어노테이션(그 밖에 @Service, @Repository, @Controller, @Configuration 도 포함)이 붙어있는 객체들을 스프링 빈으로 등록한다.
