---
title: "ConstraintLayout을 더 완벽하게 만들어 줄 2가지 도구"
description: "Guideline과 Barrier"
date: "2020-06-15T14:22:58.394Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/constraintlayout%EC%9D%84-%EB%8D%94-%EC%99%84%EB%B2%BD%ED%95%98%EA%B2%8C-%EB%A7%8C%EB%93%A4%EC%96%B4-%EC%A4%84-2%EA%B0%80%EC%A7%80-%EB%8F%84%EA%B5%AC-15f51418e6d0
redirect_from:
  - /constraintlayout을-더-완벽하게-만들어-줄-2가지-도구-15f51418e6d0
---

### 1\. Guideline

Guideline은 화면의 가장자리 안에 또 다른 가장자리를 만들어 준다고 생각하면 됩니다.

Guideline은 레이아웃에 수직이나 수평으로 긋는 하나의 직선입니다. 그리고 view들을 해당 guideline에 constraint를 맞추어 주는거죠.

undefined

Guideline을 사용함으로서 view의 위치가 바뀌더라도 각각의 view나 parent에 constraint를 줬을 때 보다 훨씬 유연하게 대처가 가능합니다.

기본적인 사용법은 다음과 같습니다.

```
<androidx.constraintlayout.widget.Guideline
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_percent="0.5"
        />
```

guideline의 위치를 정하는 방법은 크게 두개로 나뉘는데 첫번째로 위 코드처럼 `layout_constraintGuide_percent` 속성을 이용하여 화면에서 몇 % 떨어져 있는지 결정하는 방법과 두번째로 `layout_constraintGuide_begin`, `layout_constraintGuide_end` 속성을 이용하여 화면의 시작 혹은 끝에서 몇 dp, px만큼 떨어져 있는지 결정하는 방법입니다.

`orientation`속성으로 guideline을 세로로 세울지 가로로 눕힐지 결정합니다.

---

### 2\. Barrier

Barrier는 여러개의 view를 하나로 묶어서 constraint를 형성합니다.

undefined

예를 들어, 위와 같이 Email과 Password 두개의 textiView가 존재합니다.

Email과 Password를 하나의 barrier에 묶어서 주변 view들과 constraint를 형성한 모습을 볼 수 있습니다.

위의 경우는 password가 더 길기때문에 password를 기준으로 barrier가 형성되었지만 아래의 경우는 email이 길어지면서 email을 기준으로 barrier가 형성되었습니다.

undefined

이 처럼 barrier는 주로 다국어지원을 하는데에 유용하게 사용됩니다. 각 언어에 따라 text의 길이가 달라지기 때문입니다.

구체적인 사용방법은 다음과 같습니다.

```
<androidx.constraintlayout.widget.Barrier
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            app:constraint_referenced_ids="Text_1, Text_2"
            app:barrierDirection="end"
            />
```

constraint\_referenced\_ids 속성은 해당 barrier가 어떤 view들을 포함하는 지를 명시합니다. barrier로 포함하고 싶은 view들을 콤마(,)로 구분하여 작성하면 됩니다. 위의 경우는 Text\_1과 Text\_2를 barrier로 묶은거죠.

barrierDirection은 속성은 barrier가 해당 view들에 대하여 어느 방향으로 형성 될 것인지를 명시합니다. 위의 경우는 Text\_1과 Text\_2 중 더 길이가 긴 view의 오른쪽으로(end) barrier가 형성될 것입니다.
