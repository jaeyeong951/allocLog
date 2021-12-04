---
title: "[AWS Kinesis] Data Stream vs. Data Firehose"
description: "AWS Kinesis 의 Data Stream 과 Data Firehose 의 차이점"
date: "2021-09-23T17:47:40.880Z"
categories: []
published: true
canonical_link: https://medium.com/@jaeyeong951/aws-kinesis-data-stream-vs-data-firehose-10102d949741
redirect_from:
  - /aws-kinesis-data-stream-vs-data-firehose-10102d949741
---

undefined

### AWS Kinesis

Kinesis 란 AWS 에서 제공하는 실시간 데이터 처리, 분석 시스템으로 세부적인 기능에 따라 4개의 서비스로 나뉩니다.

-   Kinesis Data Stream
-   Kinesis Date Firehose (혹은 Delievery Stream)
-   Kinesis Video Stream
-   Kinesis Data Analytics

위 4가지 중 Kinesis Video Stream 은 사용자 디바이스에서 AWS로 비디오 데이터를 쉽고 안전하게 스트리밍할 수 있게 도와주는 서비스이고, Kinesis Data Analytics 는 실시간으로 스트리밍 데이터를 필터링, 집계 및 변환하는 고급 분석 기능을 제공하는 서비스 입니다.

이처럼 Video Stream 과 Data Analytics 이름만 봐도 알 수 있을 정도로 역할 구분이 명확한 것에 비해 Data Stream 과 Date Firehose 는 그 차이점을 한눈에 파악하기가 쉽지 않습니다.

그래서 이 둘의 차이를 간단히 정리해보고자 합니다.

### Kinesis Data Stream

undefinedundefined

Kinesis Data Stream 은 실시간으로 data 들을 받아들일 수 있는 입구이자 저장소로서의 역할을 합니다.

한 시스템이 실시간으로 Data Stream 에 데이터를 전송하면 해당 Data Stream 을 listening 하고있던 다른 한 시스템이 해당 데이터를 받아 처리하는 것 입니다.

pipeline 이자 메시지 큐와 비슷한 역할을 한다고 볼수도 있겠네요.

### Kinesis Data Firehose (Delivery Stream)

undefined

Kinesis Data Firehose 의 주목적은 미리 정의된 목적지(Destination)에 데이터를 안전하게 전달(Deliver)하는 것입니다.

여기서 목적지라 함은 S3 bucket, ElasticSearch, Amazon Redshift 등이 data lake의 역할을 할 수 있는 다양한 저장소를 말합니다.

Date Firehose 는 데이터를 그저 전달하는 것 뿐만 아니라 람다를 이용해 가공하는 작업도 가능합니다.

### 비교

-   Data Stream 은 **low latency streaming service** 이고, Firehose 는 **data transfer service 입니다.**
-   Data Stream 은 길게는 일주일 까지 데이터를 잠시 저장할 수 있지만 (Stream 의 역할을 하기 때문에), Firehose 는 데이터를 잠시라도 들고있을 수 없습니다. (말그대로 Transfer 의 역할만 수행)
-   Data Stream 은 Consumer 들이 Stream 에서 데이터를 꺼내와서 작업한다는 개념이고 Firehose 는 데이터를 직접 Destination 에 전해다주는 개념입니다.
-   Data Stream 은 Consumer 를 여러개 지정할 수 있으며 open-ended model 인 반면, Firehose는 단일 Destination 을 가지는 close-ended 모델입니다.
-   Data Stream 은 샤드 수를 조정하여 수동으로 Scailing 해야하는 반면 Firehose 는 데이터 요청에 따라 Scailing 이 자동으로 이루어집니다.

---

### Reference

[Amazon Kinesis Data Firehose — 스트리밍 데이터 파이프라인 — Amazon Web Services](https://aws.amazon.com/ko/kinesis/data-firehose/?kinesis-blogs.sort-by=item.additionalFields.createdDate&kinesis-blogs.sort-order=desc)

[Amazon Kinesis Data Streams — 데이터 스트리밍 서비스 — Amazon Web Services](https://aws.amazon.com/ko/kinesis/data-streams/)

[AWS Kinesis Data Streams vs AWS Kinesis Data Firehose — Whizlabs Blog](https://www.whizlabs.com/blog/aws-kinesis-data-streams-vs-aws-kinesis-data-firehose/)
