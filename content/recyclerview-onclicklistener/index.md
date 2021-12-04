---
title: "RecyclerView의 onClickListener 구현"
description: "다만, 어떤 기능들은 저런 방식으로는 구현이 불가능합니다.\n예를 들면 아이템을 클릭했을 때 발생시킬 이벤트에 필요한 데이터가 viewModel이나 view에 있는 경우, 혹은 클릭 시 fragment간 화면 전환을 위한 navigation이 필요"
date: "2020-07-13T13:12:09.671Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/recyclerview%EC%9D%98-onclicklistener-%EA%B5%AC%ED%98%84-c1c4bf16e90f
redirect_from:
  - /recyclerview의-onclicklistener-구현-c1c4bf16e90f
---

undefined

대부분의 경우 RecyclerView의 아이템에 onClickListener를 구현할 경우 다음과 같이 onBindViewHolder에 로직을 넣습니다.

adapter.kt

위 같이 작성해도 사실 큰 문제는 없습니다.

다만, 어떤 기능들은 저런 방식으로는 구현이 불가능합니다.

예를 들면 아이템을 클릭했을 때 발생시킬 이벤트에 필요한 데이터가 viewModel이나 view에 있는 경우, 혹은 클릭 시 fragment간 화면 전환을 위한 navigation이 필요한 경우 등이있습니다.

이런 경우 onClick의 구현부는 View에 있는 것이 바람직합니다.

adapter.kt

위 코드의 경우 adapter는 clickListener interface를 parameter로 받고 그 구현부는 View에서 adapter 생성 시에 정의합니다.

view.kt

예전부터 recyclerview의 onclicklistener는 정확히 어디에 구현하는게 맞는가? 라는 주제로 여러 개발 커뮤니티에서 자주 논란이 되어 왔습니다.

완벽하게 이게 정답이야! 라는 방식은 없으니 자신이 구현하고싶은 비즈니스 로직에 맞는 방법으로 사용하면 되겠습니다.

위 방법 또한 그 중 하나일 뿐이니 더 좋은 방법이 있을지 생각해보면 좋겠습니다.
