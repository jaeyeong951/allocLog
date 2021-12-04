---
title: "[Android] COIL : 안드로이드 Image Loader"
description: "안드로이드의 새로운 Image Loading Library"
date: "2020-06-16T08:50:00.087Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/android-coil-%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-image-loader-e989aaa95ce5
redirect_from:
  - /android-coil-안드로이드-image-loader-e989aaa95ce5
---

undefined

### 소개

현재 안드로이드 애플리케이션에서 Image를 불러와 ImageView에 적용시키기위해 사용하는 라이브러리는 수없이 많지만 가장 많이 쓰이는 라이브러리로는 Glide, Picasso, Universal Image Loader 등이 있습니다. 이 라이브러리들은 안드로이드의 주 개발언어가 Java인 시절부터 존재했던 연혁이 꽤나 긴 친구들입니다. 최근 Kotlin이 급부상하면서 새로운 image loading 라이브러리가 등장했는데 그게 바로 COIL입니다. COIL은 **CO**routine **I**mage **L**oader의 앞글자를 따서 지어진 이름입니다. 이름만 보셔도 아실 수 있듯이 Kotlin Coroutine을 사용하여 만들어진 라이브러리입니다. 하지만 COIL을 사용하기위해 Coroutine을 반드시 알아야하는 건 아닙니다. 몰라도 전혀 상관없습니다. COIL의 주요 기능과 사용법을 천천히 알아보겠습니다.

---

### 장점

모든 라이브러리가 각자의 장점이 있듯, COIL도 다른 라이브러리와 구분되는 자신만의 강점이 존재합니다.

-   **Fast** : COIL은 메모리, 디스크 캐싱(Caching), 이미지 다운샘플링, 비트맵 재사용 등을 수행하며 여러단계의 최적화를 수행합니다.
-   **Lightweight** : COIL은 2000개 가량의 method를 해당 애플리케이션의 apk에 추가하는데 이는 Glide나 Frecso보다 훨씬 적고 Picasso와 비슷한 수준입니다.
-   **Easy to use** : COIL은 kotlin의 장점을 적극 활용하여 훨씬 간단하고 쉬운 문법을 제공합니다.
-   **Modern** : COIL은 kotlin을 기반으로 만들어졌으며 Coroutine, OkHttp, Okio, AndroidX Lifecycle등 다양한 modern library를 사용합니다.
-   **Bitmap Pooling** : Glide나 Fresco처럼 Coil도 비트맵 풀링을 지원합니다. 비트맵 풀링이란 사용되지않는 비트맵 오브젝트를 재활용하는 테크닉을 말합니다. 이를 이용하면 메모리 성능을 상당히 개선할 수 있습니다.
-   **Image Sampling** : 500x500크기의 이미지를 불려오려면 한번에 500x500 이미지를 모두 불러와도 되지만 그 만큼 로딩시간이 소요되고 유저는 이미지 로딩이 끝날 때 까지 아무것도 보지 못합니다. 이런 경우 500x500 만큼의 이미지의 로딩이 끝날 때 까지100x100 만큼의 저화질의 이미지를 먼저 로딩해서(placeholder) 보여줍니다. COIL은 crossfade(true) 옵션을 통해 해당 기능을 제공합니다. [progressive JPEG](https://www.liquidweb.com/kb/what-is-a-progressive-jpeg/)처럼 당장에는 저화질 이미지로 보이지만 로딩이 끝나면 고화질 이미지로 자연스럽게 변하는 동작을 수행합니다.

---

### 사용법

#### 요구사항

COIL을 사용하기에 앞서서, 먼저 아래와 같은 개발 조건은 만족해야합니다.

-   Android X
-   Min SDK 14+
-   Compile SDK: 29+
-   Java 8+

#### Dependency 추가

앱 단위 **build.gradle** 파일에 다음 코드를 추가합니다.

```
implementation "io.coil-kt:coil:0.11.0"
```

#### 이미지 불러오기

ImageView에 이미지를 불러오기위해 .load 함수를 사용합니다.

```
// URL
imageView.load("<https://imageofworld.com/image/phonewhite.png>")
// Resource
imageView.load(R.drawable.image)
// File
imageView.load(File("/path/to/image.jpg"))
// And more...
```

#### 이미지를 간단히 편집하기

COIL은 Blur효과나 원형으로 자르는 등의 간단한 편집 기능을 제공합니다.

-   **CircleCropTransformation —** 이미지를 원형으로 자릅니다.
-   **RoundedCornersTransformation —** 이미지의 가장자리를 둥글게 처리합니다.
-   **BlurTransformation —** blur효과를 씌웁니다.
-   **GrayscaleTransformation —** 이미지에 greyscale효과를 씌웁니다.

```
imageView.load("<https://imageofworld.com/image/phonewhite.png>") {
    crossfade(true)
    placeholder(R.drawable.place_holder_image)
    transformations(CircleCropTransformation())
}
```

### Image Loaders

이미지를 로드하기 위한 **.load** 함수는 내부적으로 **LoadRequest** 함수를 실행하기 위해 singleton으로 생성된 **ImageLoader** 객체를 사용합니다. ImageLoader는 다음과 같이 접근할 수 있습니다.

```
val imageLoader = Coil.imageLoader(context)
```

ImageLoader를 따로 생성하여 의존성을 주입시킬 수도 있습니다.

```
val imageLoader = ImageLoader(context)
```

### Requests

이미지를 custom target에 로드하기 위해선 내부적으로 **LoadRequest 함수**를 실행합니다.

```
val request = LoadRequest.Builder(context)
    .data("<https://imageofworld.com/image/phonewhite.png>")
    .target { drawable ->
        // target ImageView
				// 기본적으로는 this(ImageView)
    }
    .build()
imageLoader.execute(request)
```

이미지를 imperative하게 가져오려면 **GetRequest**를 사용합니다.

```
val request = GetRequest.Builder(context)
    .data("<https://imageofworld.com/image/phonewhite.png>")
    .build()
val drawable = imageLoader.execute(request).drawable
```

### 지원 가능한 Data Type

ImageLoader instance가 지원하는 data type들 입니다.

-   String (mapped to a Uri)
-   HttpUrl
-   Uri (`android.resource`, `content`, `file`, `http`, and `https` schemes only)
-   File
-   @DrawableRes Int
-   Drawable
-   Bitmap

### Preloading

이미지를 메모리에 미리 로드(preload)하기 위해, target 없이 LoadRequest를 실행합니다.

```
val request = LoadRequest.Builder(context)
    .data("<https://www.example.com/image.jpg>")
    // Optional, but setting a ViewSizeResolver will conserve memory by limiting the size the image should be preloaded into memory at.
    .size(ViewSizeResolver(imageView))
    .build()
imageLoader.execute(request)
```

network의 이미지를 디스크 캐시에 미리 로딩하기위해선 CachePolicy 옵션을 disable로 설정해줍니다.

```
val request = LoadRequest.Builder(context)
    .data("<https://www.example.com/image.jpg>")
    .memoryCachePolicy(CachePolicy.DISABLED)
    .build()
imageLoader.execute(request)
```

### Cancelling Requests

LoadRequest는 View가 detached 되거나 연관된 LifeCycle이 종료되거나 혹은 같은 View에서 다른 request가 시작되었을 때 자동적으로 취소됩니다.

조금 더 자세히 들여다보면, 각각의 LoadRequest는 RequestDisposable을 반환하는데 이는 request가 progress 상태인지 request가 끝난 dispose 상태인지 확인합니다.

RequestDisposable은 내부적으로 다음과 같이 작성되어 있습니다.

```
interface RequestDisposable {

    /**
     * Returns true if the request is complete or cancelling.
     */
    val isDisposed: Boolean

    /**
     * Cancels any in progress work and frees any resources associated with this request. This method is idempotent.
     */
    fun dispose()

    /**
     * Suspends until any in progress work completes.
     */
    @ExperimentalCoilApi
    suspend fun await()
}
```

아래와 같이 .dispose() 함수를 사용하여 원하는 타이밍에 dispose하는 것도 가능합니다.

```
val disposable = imageView.load("<https://www.example.com/image.jpg>")

// Cancel the request.
disposable.dispose()
```

GetRequest는 연관된 coroutine context의 작업이 종료되었을 때 자동적으로 취소됩니다.

### R8 / Proguard

COIL은 R8와 완벽하게 호환되며 다른 rule을 필요로하지 않습니다.

현재 Proguard를 사용중이라면, Coroutine, OkHttp, Okio에 관한 rule은 필요합니다.

### Summary

지금까지 COIL의 특징과 기본적인 사용법에 대해 COIL 공식 document를 참고하여 두서없이 써봤습니다. 지금도 널리 쓰이는 Picasso나 Glide와 같은 다른 ImageLoading 라이브러리를 애플리케이션에 그대로 사용하여도 전혀 문제가 없겠으나 조금 더 가볍고, 빠르며, 상대적으로 문법이 간결한 COIL 라이브러리를 활용해 보는것도 좋겠습니다.

더 자세한 정보는 COIL 공식 document나 github repository를 참고해보시길 바랍니다.

[COIL Documentation](https://coil-kt.github.io/coil/getting_started/)

[COIL GitHub Repository](https://github.com/coil-kt/coil)
