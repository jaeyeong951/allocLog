---
title: "[android / kotlin] 공공 api를 사용하여 얻은 json 파싱하기"
description: "공공데이터포털의 도로위험지수 api를 사용하였습니다."
date: "2020-01-21T15:49:55.802Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/android-kotlin-%EA%B3%B5%EA%B3%B5-api%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-%EC%96%BB%EC%9D%80-json-%ED%8C%8C%EC%8B%B1%ED%95%98%EA%B8%B0-1628fbc938dc
redirect_from:
  - /android-kotlin-공공-api를-사용하여-얻은-json-파싱하기-1628fbc938dc
---

undefined

공공데이터포털의 도로위험지수 api를 사용하였습니다.

시작 좌표와 끝 좌표를 넘겨주면 해당 도로의 도로위험지수를 돌려줍니다.

작은 길가나 골목의 경우 api가 지원을 안해서

```
{
    "resultCode": "10",
    "resultMsg": "INVALID_REQUEST_PARAMETER_ERROR"
}
```

이렇게 “INVALID\_REQUEST\_PARAMETER\_ERROR” 메시지를 출력합니다.

제대로 된 좌표로 요청할 경우

```
{
    "resultCode": "00",
    "resultMsg": "NORMAL_CODE",
    "items": {
        "item": [
            {
                "index": 1,
                "line_string": "(129.066456 35.208912, 129.066456 35.208912)",
                "anals_value": "0.00",
                "anals_grd": "01"
            }
        ]
    },
    "totalCount": 1,
    "numOfRows": 1,
    "pageNo": 1
}
```

이렇게 제대로 된 값을 줍니다.

저는 anals\_grd만 쓰도록 하겠습니다.

api 통신을 위해 Asynctask를 사용하였습니다.

코드가 난잡하지만 이해바랍니다.

처음에는 그저 단순하게 받은 json에서 그대로 get(“anals\_grd”) 했더니 “anals\_grd” 값을 찾을 수 없답니다.

자세히 보니 items 하위에 item이 Array입니다.

그래서 item을 따로 JSONArray로 빼준 후 다시 get(“anals\_grd”) 하여 제대로 위험지수를 가져올 수 있었습니다.
