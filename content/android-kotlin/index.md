---
title: "[android / kotlin] 로딩 애니메이션 구현"
description: "[Android] 애니메이션의 엘리먼트와 속성"
date: "2020-01-14T06:28:30.716Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/android-kotlin-%EB%A1%9C%EB%94%A9-%EC%95%A0%EB%8B%88%EB%A9%94%EC%9D%B4%EC%85%98-%EA%B5%AC%ED%98%84-f2c6c6de37c8
redirect_from:
  - /android-kotlin-로딩-애니메이션-구현-f2c6c6de37c8
---

res/anim 에 loading.xml생성

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="<http://schemas.android.com/apk/res/android>">
    <scale
        android:duration="400"
        android:fromXScale="1.0"
        android:fromYScale="1.0"
        android:interpolator="@android:anim/accelerate_decelerate_interpolator"
        android:pivotX="50%"
        android:pivotY="50%"
        android:repeatCount="infinite"
        android:repeatMode="reverse"
        android:toXScale="-1.0"
        android:toYScale="1.0" />
</set>
```

[\[Android\] 애니메이션의 엘리먼트와 속성](https://ggoreb.tistory.com/8)

애니메이션에 관한 자세한 속성은 위의 블로그를 참조

애니메이션이 구현되었으니 로딩창을 만듭니다. (loading\_activity.xml)

```
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:app="<http://schemas.android.com/apk/res-auto>"
    xmlns:android="<http://schemas.android.com/apk/res/android>"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ImageView
        android:id="@+id/loadingIcon"
        android:layout_width="150dp"
        android:layout_height="wrap_content"
        android:src="@drawable/ib"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

</androidx.constraintlayout.widget.ConstraintLayout>
```

constraint layout을 사용하여 화면 중앙에 image를 넣었습니다.

이제 로딩창을 보여줄 액티비티 입니다.

```
package com.example.sendymapdemo

import android.app.Dialog
import android.content.Context
import android.graphics.Color
import android.graphics.drawable.ColorDrawable
import android.os.Bundle
import android.view.Window
import android.view.animation.Animation
import android.view.animation.AnimationUtils
import android.widget.ImageView

class loadingActivity(context : Context) : Dialog(context){
    private var c: Context? = null
    init{
        requestWindowFeature(Window.FEATURE_NO_TITLE)
        window!!.setBackgroundDrawable(ColorDrawable(Color.TRANSPARENT))
        setCanceledOnTouchOutside(false)
        c = context
    }
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.loading_activity)
        val logo = findViewById<ImageView>(R.id.loadingIcon)
        val animation : Animation = AnimationUtils.loadAnimation(c,R.anim.loading)
        logo.animation = animation
    }

    override fun show(){
        super.show()
    }

    override fun dismiss() {
        super.dismiss()
    }

}
```

로딩 액티비티에 타이틀바는 불필요하니 없애고 배경은 투명하게 처리합니다.

이제 이 로딩 액티비티를 보여주고 싶은 시점에

```
customLoading = loadingActivity(this)
customLoading.show()
```

를 사용하면 로딩애니메이션이 잘 작동하는걸 볼 수 있습니다.
