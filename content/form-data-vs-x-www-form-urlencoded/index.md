---
title: "form-data vs. x-www-form-urlencoded"
description: "If you have binary data (or a significantly sized payload) to transmit, use multipart/form-data. Otherwise, use x-www-form-urlencoded"
date: "2021-07-06T04:52:43.725Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/form-data-vs-x-www-form-urlencoded-311bf28a917a
redirect_from:
  - /form-data-vs-x-www-form-urlencoded-311bf28a917a
---

Photo by [Paolo Chiabrando](https://unsplash.com/@chiabra?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

> “The moral of the story is, if you have binary (non-alphanumeric) data (or a significantly sized payload) to transmit, use multipart/form-data. Otherwise, use application/x-www-form-urlencoded.”

#### **TL;DR**

HTTP Request에서 Content-type과 관련하여  
간단한 텍스트 형태의 데이터는 x-www-form-urlencoded,   
그 외에 이미지나 영상같은 raw binary data는 form-data를 사용하자.

### x-www-form-urlencoded

-   Header  
    `Content-Type: application/x-www-form-urlencoded`
-   Body  
    `key1=value1key2=value2`
-   앰퍼샌드(&)로 구분된 하나의 큰 String으로 서버에 전달 됨
-   non-alphanumeric(알파벳이 아닌 문자)는 ‘%HH’의 형식으로 치환됨
-   이말인 즉슨, 알파벳이 아닌 모든 데이터는 각각이 3바이트씩 차지하는 셈 → 굉장히 비효율적 → multipart/form-data가 생겨난 이유

### form-data

-   Header  
    `content-type: multipart/form-data; boundary=--------------------------590299136414163472038474`(boundary-string은 브라우저가 랜덤하게 생성)
-   Body  
    `key1=value1key2=value2`
-   x-www-form-urlencoded 처럼 key=value 형식으로 데이터를 전달하지만
-   각각의 key-value pair는 MIME 메시지의 part 로 나뉘어짐
-   각각의 part는 string 형식의 boundary로 나누며 (value와 겹치지않게 생성됨), 각각의 part는 MIME header를 가진다.(Content-type, Content-Disposition)
-   MIME header를 가짐으로서 key-value pair에서 value 데이터를 나타내는 방식이더욱 자유로워짐
-   **그래서 데이터마다 더욱 효율적인 encoding 방식을 적용할 수 있음.**
-   그럼 모든 데이터 전송은 form-data로 하면 되는것 아닌가?
-   간단한 알파벳형식의 텍스트 전송은 MIME header를 붙이는데 드는 overhead가 binary encoding으로 절약하는 비용보다 더 크다.
-   그래서 간단한 텍스트 전송은 x-www-form-urlencoded 으로 해결한다.

---

[form-data vs -urlencoded](https://gist.github.com/joyrexus/524c7e811e4abf9afe56)

[Postman Chrome: What is the difference between form-data, x-www-form-urlencoded and raw](https://stackoverflow.com/questions/26723467/postman-chrome-what-is-the-difference-between-form-data-x-www-form-urlencoded)
