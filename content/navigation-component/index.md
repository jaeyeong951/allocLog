---
title: "Navigation Component의 결과값 전달하기"
description: "기존 Navigation Component가 부족하다고 느껴진 점중 하나는 fragment간의 이동과 동시에 데이터 전달은 argument를 통해 가능하나, Activity의 onActivityResult 처럼 호출한 fragment에서 결과값을…"
date: "2020-07-14T11:33:20.257Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/navigation-component%EC%9D%98-%EA%B2%B0%EA%B3%BC%EA%B0%92-%EC%A0%84%EB%8B%AC%ED%95%98%EA%B8%B0-c10e795d6858
redirect_from:
  - /navigation-component의-결과값-전달하기-c10e795d6858
---

기존 Navigation Component가 부족하다고 느껴진 점중 하나는 fragment간의 이동과 동시에 데이터 전달은 argument를 통해 가능하나, Activity의 onActivityResult 처럼 호출한 fragment에서 결과값을 받아오는 것은 지원하지 않았다는 것입니다.

그래서 sharedViewModel을 통해 값을 공유하고 fragment에서 observe하는 꼼수를 쓰기도 했습니다.

하지만 이제는 그럴 필요없이 NavBackStackEntry와 LiveData를 활용하여 fragment간의 결과값 전달이 가능해 졌습니다.

올해 1월 즈음 Navigation Architecture가 2.2.0 버전으로 업데이트 되면서 [NavController.getBackStackEntry()](https://developer.android.com/reference/androidx/navigation/NavController#getBackStackEntry%28int%29)에 destination ID값을 넘겨주는 방식으로 [NavBackStackEntry](https://developer.android.com/reference/androidx/navigation/NavBackStackEntry)를 참조할 수 있게 되었습니다.

### NavBackStackEntry

→ Navigation의 Back Stack 그 자체를 가리키는 것이 아니라 이름처럼 Back Stack의 entry 역할을 합니다.

그리고 2.3.0 버전부터 NavBackStackEntry로 해당 entry의 [SavedStateHandle](https://developer.android.com/reference/androidx/lifecycle/SavedStateHandle)에 접근할 수 있게 되었습니다.

### SavedStateHandle

→ SavedStateHandle은 viewModel의 상태를 저장해놓은 Saved State를 다루기 위한 객체입니다. Key-Value 형태의 Map 구조이며, Saved State에 값을 쓰거나 읽을 수 있습니다.

다음과 같이 Frag\_1에서 Frag\_2로 값을 넘겨주는 것은 argument를 통해 간단히 구현 가능합니다.

undefined

Frag\_2에서 Frag\_1으로 결과값을 넘겨주는 동작은 다음과 같은 순서로 진행됩니다.

1.  Frag\_2를 pop한다.
2.  SavedStateHandle로 key와 result 값을 set해준다.
3.  Frag\_2가 pop되었으니 stack의 맨 위인 Frag\_1이 다시 실행된다.
4.  Frag\_1은 SavedStateHandle의 `getLiveData<T>(key)` 메소드를 통해 Frag\_2가 set한 result값을 observe 가능하다!

코드로 조금 더 자세히 살펴보겠습니다.

Frag\_2는 위와 같이 previousBackStackEntry로 자기 바로 전의 back stack에 접근하거나 getBackStackEntry 메소드로 명시적으로 어떤 back stack에 접근할 지 정할 수 있습니다.

그리고 savedStateHandle.set() 메소드로 전달하고싶은 key와 result 값을 씁니다.

Frag\_1은 위 처럼 currentBackStackEntry로 자기 자신의 stack에 접근하고 `savedStateHandle?.getLiveData<T>(key)` 메소드로 해당 key의 result 값을 가지고 있는 LiveData에 접근하여 observe 합니다.

간단히 정리하면 다음과 같습니다. 지저분하지만 양해 바랍니다.

undefined
