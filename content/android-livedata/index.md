---
title: "Android LiveData 분석"
description: "LiveData는 Google I/O 2017에서 공개된 Android Architecture Components(AAC)의 일부입니다. AAC는 간단히 말하면 안드로이드 개발의 편의를 위한 새로운 라이브러리들로 LivaData를 포함하여 총…"
date: "2020-07-23T01:52:37.201Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/android-livedata-%EB%B6%84%EC%84%9D-d8846b49efc9
redirect_from:
  - /android-livedata-분석-d8846b49efc9
---

undefined

`LiveData`는 Google I/O 2017에서 공개된 Android Architecture Components(AAC)의 일부입니다. AAC는 간단히 말하면 안드로이드 개발의 편의를 위한 새로운 라이브러리들로 LivaData를 포함하여 총 5개로 구성됩니다. AAC에 관한 자세한 설명은 이후의 포스팅에서 하나하나 다루도록 하겠습니다.

Google Developer 공식 문서에 따르면 LiveData의 정의는 다음과 같습니다.

> **_LiveData_** _is an observable data holder class. Unlike a regular observable, LiveData is lifecycle-aware, meaning it respects the lifecycle of other app components, such as activities, fragments, or services. This awareness ensures LiveData only updates app component observers that are in an active lifecycle state._

글만 봐서는 어떤 용도로 사용되는지 감이 잘 안잡힙니다.

간단히 설명하면 LiveData는 안드로이드에서 `Observer Pattern을 쉽고 간단한 문법으로 구현`할 수 있게 해줍니다. 주로 ViewModel에 생성하며 View가 observe하는 형태로 사용합니다.

### Observer Pattern?

객체지향적인 설계를 하다보면 객체간의 데이터 처리를 하는 경우가 많습니다. 비단 안드로이드 뿐만 아니라 어떤 프로그램이든 data를 생산하는 data source가 있고 그 data를 보여줄 view가 있기 마련입니다. 이 때 data의 흐름은 2가지가 있을 수 있겠죠.

1.  View가 Data Source에게 data를 달라고 명시적으로 요청
2.  Data Source는 Data를 생산하고 View는 Data를 기다림

이 두가지 흐름 중 2번의 경우가 Observer Pattern에 해당합니다. Data를 가지고 있는 주체 객체(Data Source)와 Data의 변경을 감지하고 사용해야하는 관찰자 객체(Observer)가 존재하며 이 들의 관계는 1 : 1 이거나 1 : N이 될 수 있습니다.

이렇게 Data를 주는 과정에서 객체의 크기가 커지거나 객체들의 관계가 복잡해질 수록 코드 또한 복잡해질 겁니다. 이 복잡도를 줄이기 위해 제시된 프로그래밍 가이드라인이 Observer Pattern입니다.

하지만 Observer Pattern의 구현뿐만 아니라 위 공식 도큐먼트의 정의에서 말하듯 LiveData는 `lifecycle-aware component`입니다. 조금더 정확히 말하자면, LiveData의 lifecycle은 observer의 lifecycle을 따라갑니다. 그래서 observer가 위치한 View가 사라지면 LiveData도 같이 사라지는거죠.

이런 특성은 LiveData를 구현한 코드를 보면 바로 이해하실 수 있습니다.

먼저 ViewModel에서 LiveData를 생성해보죠.

viewModel

LiveData를 \_data와 data로 나누는 이유는 ViewModel만 \_data를 이용해 LiveData를 수정할 수 있고 View는 data만 접근가능하여 수정을 불가능하게 하기 위함입니다.

LiveData가 값을 전달하는 방법은 postValue와 setValue 두가지가 있는데 조금 차이가 있습니다. 두 방식 모두 내부 코드를 보고 이해해보죠.

먼저 postValue를 봅시다.

postValue

postValue는 값을 set하는 runnable 객체를 main thread에 던집니다. 그래서 만약 LiveData.postValue(“a”) 다음 LiveData.setValue(“b”)를 호출하면 값은 먼저 “b”로 set된 후 main thread에 의해 “a”로 덮어집니다.

main thread에서 즉각적으로 실행하는 것이 아니라 task를 던져준것이기 때문에 현재 main thread의 task가 끝나야 실행되는 것이죠.

그래서 postValue는 여러번 수행해도 맨 마지막 동작만 실질적으로 반영됩니다.

다음은 setValue 입니다.

setValue

반면에 setValue는 main thread에서 set동작을 즉각적으로 수행합니다. 그래서 값의 변화또한 그와 동시에 전달 됩니다.

위와 같은 과정을 통해 LiveData를 생성했다면 이제 View에서 관찰해야겠죠.

View

View에서는 viewModel의 data를 가져와서 observe해주면 됩니다.

observe의 인자는 총 2개인데, 전자는 LifeCycleOwner이고 후자는 Observer입니다.

LifeCycleOwner란 해당 activity나 fragment의 lifecycle을 분리하여 담은 객체를 말합니다. 한마디로 해당화면의 생명주기를 모니터링할 수 있는 것이죠. LiveData는 LifeCycleOwner를 통해 해당 View의 lifeCycle을 관찰하고 해당 View와 생명주기를 같이할 수 있는것이죠. 위 코드에서는 해당 view의 LifeCycleOwner는 곧 자신이기 떄문에 this로 전달했습니다.

Observer는 해당 data를 가져와서 사용할 로직을 정의하는 interface 객체입니다. 가져온 data를 어떻게 사용할 지 정의하면 됩니다.

---

LiveData에 대해 간단하게나마 알아보았습니다. AAC의 LifeCycle, ViewModel, LiveData와 같은 유용한 라이브러리를 통해 개발이 한층 쉬워졌습니다. 기본적인 사용법 숙지도 중요하지만 어떤 의도로 만들어 졌는지, 이를 활용하여 내 코드를 어떻게 더 발전시킬 수 있을지 그리고 내부적으로 어떻게 동작하는지 너무 자세히는 아니더라도 간단히는 알아보는 것도 좋겠습니다.
