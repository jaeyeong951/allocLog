---
title: "그렇다면 repository는 싱글톤으로 생성하지만 viewModel은 그렇지 않고 view의 인스턴스 생성시점에 따라 lazy하게 로딩한다는 거군요."
description: ""
date: "2021-06-13T14:33:10.519Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/%EA%B7%B8%EB%A0%87%EB%8B%A4%EB%A9%B4-repository%EB%8A%94-%EC%8B%B1%EA%B8%80%ED%86%A4%EC%9C%BC%EB%A1%9C-%EC%83%9D%EC%84%B1%ED%95%98%EC%A7%80%EB%A7%8C-viewmodel%EC%9D%80-%EA%B7%B8%EB%A0%87%EC%A7%80-%EC%95%8A%EA%B3%A0-view%EC%9D%98-%EC%9D%B8%EC%8A%A4%ED%84%B4%EC%8A%A4-%EC%83%9D%EC%84%B1%EC%8B%9C%EC%A0%90%EC%97%90-%EB%94%B0%EB%9D%BC-lazy%ED%95%98%EA%B2%8C-%EB%A1%9C%EB%94%A9%ED%95%9C%EB%8B%A4%EB%8A%94-%EA%B1%B0%EA%B5%B0%EC%9A%94-313c069fb8a8
redirect_from:
  - /그렇다면-repository는-싱글톤으로-생성하지만-viewmodel은-그렇지-않고-view의-인스턴스-생성시점에-따라-lazy하게-로딩한다는-거군요-313c069fb8a8
---

그렇다면 repository는 싱글톤으로 생성하지만 viewModel은 그렇지 않고 view의 인스턴스 생성시점에 따라 lazy하게 로딩한다는 거군요. 그렇다면 결국 viewModelFactory에게 viewModel 생성을 위임한다는 말이 될텐데 viewModel의 생성에서 hilt가 하는 역할이 정확이 어떤건가요? repository는 싱글톤으로 hilt module이 관리해주지만 viewModel은 말씀하신 것처럼 lazy하게 로딩한다면 hilt가 viewModel 생성과정에서 어떤 역할을 해주는건지 모르겠습니다.
