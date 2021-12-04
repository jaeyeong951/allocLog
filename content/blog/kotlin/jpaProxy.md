---
title: 'JPA와 프록시 객체(1:N, Cascade)'
date: 2021-11-22 02:12:11
category: 'data'
draft: false
---

![jpa.png](https://res.craft.do/user/full/8884c80f-6eec-6a29-2a03-049def967beb/doc/48072C92-AC1F-481F-A047-8477FAEF461E/77F0307B-A600-4A7E-9FEF-33E84B9575EF_2/jpa.png)

# JPA 프록시

Member를 조회할 때 Team도 같이 조회해야 할까?

![Image.png](https://miro.medium.com/max/1400/0*zXbd4xFK1r87-qbc)

멤버를 조회하는 데 팀을 굳이 가져올 필요가 없다고 해보자. → 비즈니스 로직상 멤버의 이름만 출력하는 메소드가 존재한다면? → 지연 로딩과 즉시 로딩에 대해 고민

## 프록시 기초

- JPA 프록시란? **빈 껍데기에 id값만 들고있는 가짜 객체**
- em.find() vs em.getReference()
- em.find() : DB를 통해서 실제 엔티티 객체 조회
- em.getReference() : DB 조회를 미루는 가짜(프록시) 엔티티 객체 조회

```java
Member member = new Member();
member.setusername("hello");
em.persist(member);
em.flush();
em.clear();
Member findMember = em.getReference(Member.class, member.getId());
//이 시점에 멤버를 찾는 쿼리가 나가지 않음 -> 프록시 클래스
System.out.println("findMember.id = " + findMember.getId());
//id는 이미 안다. -> 이 때도 아직 쿼리가 안나감
System.out.println("findMember.username = " + findMember.getUsername());
//이 시점에 쿼리가 나감 -> user 찾기
```

## 프록시 특징

- 실제 클래스를 상속 받아서 만들어진다.
- 실제 클래스와 겉 모양이 같다.
- 사용하는 입장에서는 진짜 객체인지 프록시 객체인지 구분하지 않고 사용해도 된다.(이론상)

![Image.png](https://miro.medium.com/max/972/0*csDniP_lvlYwUD1a)

- 프록시 객체는 실제 객체의 참조를 보관

![Image.png](https://miro.medium.com/max/1400/0*peYzH63MxyAI_D2B)

- 프록시 객체를 호출하면 프록시 객체는 실제 객체의 메소드를 호출

## 프록시 객체의 초기화

위의 코드 예제에서 member.getName() 수행 시 어떤 과정을 통해 프록시 객체가 실제 객체를 불러오는걸까?

![Image.png](https://miro.medium.com/max/1400/0*p9Wa3SVJATHPQU36)

1. 처음에 프록시 객체에 Member target이 null임.
2. JPA가 영속성 컨텍스트에 진짜 객체를 가져오라 명령. (초기화 요청)
3. DB를 조회하여 실제 객체를 생성
4. 기존 프록시 객체와 진짜 객체를 연결
5. 그래서 프록시의 getName()은 진짜 객체의 getName()을 호출할 수 있는 것.

# 프록시의 중요한 특징들

- 프록시 객체는 처음 사용할 때 한번만 초기화
- 프록시 객체를 초기화 할 때, **프록시 객체가 실제 엔티티로 바뀌는 것이 아님!** → 초기화 되면 target을 통해 실제 엔티티에 접근이 가능해 지는 것
- 프록시 객체는 원본 엔티티를 상속 받음 → 타입 체크시 유의 → ‘==’ 대신 instance of를 사용하자.
- **영속성 컨텍스트에 찾는 엔티티가 이미 있으면 `em.getReference()`를 호출해도 실제 엔티티를 반환**
   - 이미 영속 컨텍스트(1차 캐시)에 있는 것을 프록시로 불러와봐야 아무런 성능상의 이점이 없음
   - 반대도 마찬가지. 처음에 프록시로 불러오면 똑같은 객체를 find로 실제 객체를 호출해도 프록시 객체를 반환함.

```java
Member refMember = em.getReference(Member.class, member1.getId()); // Proxy

Member findMember = em.find(Member.class, member1.getId()); // Member

System.out.println("refMember  findMember: " + (refMember  findMember)); // 결과는?

// false가 나와야 할 것 같으나 JPA는 이런 경우 true를 보장해야함
```

- 위 코드의 결과는 true이고 refMember와 findMember가 둘 다 프록시임.
- 한번 프록시로 조회해버리면 `em.find`를 해도 실제 객체를 조회하는 건 맞으나 실제 객체가 아니라 프록시 객체를 반환해버림
- ref == find 가 true임을 보장하기 위해서
- **영속성 컨텍스트의 도움을 받을 수 없는 준영속 상태일 때, 프록시를 초기화하면 문제 발생**

```java
Member refMember = em.getReference(Member.class, member1.getId());
em.detach(refMember); // 영속성 컨테스트가 관리 X -> 준영속
refMember.getusername(): // Could not initialize proxy 오류 발생
```

- 준영속 상태의 객체는 영속성 컨텍스트의 도움을 못받음
- 프록시 초기화가 불가능함!!

## 프록시 확인

- 프록시 인스턴스의 초기화 여부 확인
   - `emf.PersistenceUnitUtil.isLoaded(Object entity);`
- 프록시 클래스 확인 방법
   - `entity.getClass().getName()` 출력
- 프록시 강제 초기화
   - Hibernate.initialize → 쓰지 말자
   - `필요하다면 member.getName()` 처럼 사용

프록시를 실제로 잘 쓰진 않겠지만 **즉시 로딩과 지연 로딩을 이해하는데에 꼭 필요한 개념**

# 즉시 로딩과 지연 로딩

맨 처음에 나왔던 질문을 다시 보자.

Member를 조회할 때 Team도 같이 조회해야 할까?

![Image.png](https://miro.medium.com/max/60/0*q0uFx_yz0W-DVw8e?q=20)

![Image.png](https://miro.medium.com/max/1400/0*q0uFx_yz0W-DVw8e)

## 지연 로딩 fetch = FetchType.Lazy를 사용해서 프록시로 조회

```java
@Entity
public class member {
	@Id
	@GeneratedValue
	private Long id;
	@Column(name = "USERNAME")
	private String name;
	@ManyToOne(fetch = FetchType.Lazy)
	@JoinColumn(name = TEAM_ID)
	private Team team;
	...
}
```

위 처럼 fetchType을 Lazy로 주면 지연로딩을 사용함

→ **프록시 객체로 로딩한다는 의미**

맨 처음엔 Member 테이블만 가져오고 Team을 조회하면 해당 FK로 Team 테이블을 불러온다.

**멤버를 조회할 때는 딱 멤버만 가져오고 팀은 필요한 시점에 불러온다!**

## 즉시 로딩 fetch = FetchType.EAGER를 사용해서 실제 객체로 조회

비즈니스 로직상 Member와 Team을 같이 사용하는 경우가 많다면? → 굳이 지연로딩으로 따로 조회할 필요 없음 → 오히려 성능 손해

즉시 로딩을 사용하자.

```java
@Entity
public class member {
	@Id
	@GeneratedValue
	private Long id;
	@Column(name = "USERNAME")
	private String name;
	@ManuToOne(fetch = FetchType.EAGER)
	@JoinColumn(name = TEAM_ID)
	private Team team;
	...
}
```

위 처럼 fetchType을 EAGER로 주면 즉시 로딩을 사용함

Member 테이블을 불러올 때 Team 테이블도 Member 테이블과 조인하여 같이 불러옴

![Image.png](https://miro.medium.com/max/1400/0*UuE81-y6HjCSSSSt)

# 🔥프록시와 즉시로딩 사용 시 주의!

## 가급적이면 지연 로딩만 사용하자!

- 즉시 로딩을 적용하면 전혀 예상하지 못한 SQL이 발생
- 즉시 로딩은 JPQL에서 N+1 문제를 일으킨다.

```java
List members = em.createQuery("select m from Member m", Member.class).getResultList();
```

- EAGER로 설정해도 위의 경우 select 쿼리가 두번 나감 → **join 쿼리가 수행되지 않음**
   - `select * from member`
   - `select * from team where TEAM_ID = ?`
- 쿼리가 두배가 되는 것
- 물론 LAZY를 써도 멤버 수 만큼 팀을 조회한다면 그대로 쿼리가 두배로 나갈 것
- @ManyToOne, @OneToOne은 기본이 즉시 로딩 → LAZY로 설정
- @OneToMany, @ManyToMany는 기본이 지연 로딩

## 지연 로딩 활용

밑의 내용은 좀 이론적인 내용이고 어지간하면 지연 로딩을 사용하자.

- Member와 Team은 자주 함께 사용 → 즉시 로딩
- Member와 Order는 가끔 사용 → 지연 로딩

![Image.png](https://miro.medium.com/max/1400/0*o4oV8I5VE07bg-O4)

# 영속성 전이 : CASCADE

특정 엔티티를 영속 상태로 만들 때 연관된 엔티티도 함께 영속 상태로 만든다.

![Image.png](https://miro.medium.com/max/1400/0*Sfpd6jDde6_GZY5h)

```java
Child child1 = new Child();

CHild child2 = new Child();Parent parent = new Parent();

parent.addChild(child1);

parent.addChild(child2);em.persist(parent);

em.persist(child1);

em.persist(child2);
```

위 코드처럼 parent와 하위 child를 모두 영속화 해주어야 한다.

하지만 cascade 옵션을 사용하면 parent만 영속화 해줘도 child의 영속화가 자동으로 이루어지게 할 수 있다.

## CASCADE 주의할 점!

- 영속성 전이는 연관관계를 매핑하는 것과 아무런 관련이 없음
- CASCADE는 엔티티를 영속화할 때 연관된 엔티티도 함께 영속화하는 편리함을 제공할 뿐 그 이상 그 이하 아무것도 아니다.

## CASCADE 종류

- ALL : 모두 적용
- PERSIST : 영속
- REMOVE : 삭제

부모 자식 관계가 1 : N으로 명확할 때만 사용하자. → 자식의 소유자가 하나일 때 (단일 소유자)

자식을 소유한 테이블이 여러개일 때는 사용 X

## 고아 객체

- 부모의 FK를 가지고있는 자식 엔티티가 자신은 삭제되지 못한채로 부모가 먼저 삭제된 경우
- 고아 객체 제거 : 부모 엔티티와 연관 관계가 끊어진 자식 엔티티를 자동으로 삭제
- orphanRemoval = true 옵션을 사용

## 고아 객체 자동 제거 — 주의할 점!

- 참조가 제거된 엔티티는 다른 곳에서 참조하지 않는 고아 객체로 보고 삭제하는 기능
- 참조하는 곳이 딱 한 곳일때만 사용 → 특정 엔티티가 개인 소유할 때
- @OneToOne, @OneToMany 만 사용 가능
- 부모를 제거하면 자식은 고아가 된다. 따라서 고아 객체 제거 기능을 활성화 하면, 부모를 제거할 때 자식도 함께 제거된다. → CascadeType.REMOVE와 동일하게 기능한다.

## 영속성 전이 + 고아 객체? 그리고 생명주기

- (CascadeType.ALL) + (orphanRemoval = true)
- 스스로 생명주기를 관리하는 엔티티는 em.persist()로 영속화하고 em.remove()로 제거
- 위의 parent와 child의 관계를 다시 보자.
- 영속성 전이와 고아 객체 자동 제거를 모두 사용하면
- child는 명시적으로 영속화를 호출할 일이 전혀 없다.
- child객체를 생성해서 parent의 childList에 add해주고 부모만 영속화 해줘도 child는 자동으로 영속화 되고 삭제도 마찬가지
- child의 생명주기는 모두 parent가 관리하게 됨
- DB의 관점에서 보자면 child는 dao나 repository가 전혀 필요없어짐
- DDD의 Aggregate Root 개념을 구현할 때 유용하다.

Reference

- 김영한님의 [**자바 ORM 표준 JPA 프로그래밍 — 기본편**](https://www.inflearn.com/course/ORM-JPA-Basic/dashboard)

