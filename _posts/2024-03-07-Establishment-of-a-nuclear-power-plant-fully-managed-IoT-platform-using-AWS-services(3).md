---
layout: post
title: S3, Aurora 사이의 데이터 교환 
subtitle: s3 데이터를 데이터베이스에서 활용하자
categories: AWS
description: S3와 AWS Aurora MySQL 간 데이터 주고받기 (업로드 & 다운로드) Data exchange between S3 and Aurora
tags: aws s3 aurora
---

# S3와 AWS Aurora MySQL 간 데이터 주고받기

## S3의 데이터를 Aurora DB에서 활용하자.

S3는 aws 스토리지 서비스 중 하나이면서 aws 서비스 전체 중에서도 가장 많이 쓰는 서비스 중 하나로 단순한 저장소로도 쓸 수 있지만 다른 aws 서비스와 연결되어 확장성이 가장 중요한 특징입니다. 예를 들어 웹 프론트 파일을 업로드하여 정적 웹 호스팅에 쓰여지거나 그 자체로 DB처럼 쓰이거나 각종 미디어 파일 변환등의 저장소로 활용되기도 합니다.

일전에 [상권분석_프로젝트](ttps://code-y-learner.github.io/python/2024/02/13/static_data_VS_Dynamic_data.html)에서 상권 공공데이터를 통해 관계형 DB를 설계한 적이 있었는데 python-mysql를 활용하여 auroraDB 엔드포인트에 연결하여 직접 csv파일을 임포트하여 하나하나 넣어준 적이 있습니다.

```python

```

 하지만 데이터가 5년치 분량이 되가면서 행의 개수가 140만개 컬럼의 55개가 되면서 많은 데이터를 직접 쓸려고 하니 데이터 서버에 많은 쓰기 부하와 비용도 발생하였습니다.

>발전요구량 : MWh<br>
>발전량 : MWh<br>
>제어봉 높이 : m<br> / (총 7개 종류 위치가 다름)
>파이프 : MPa(압력), °C(온도) / (총 6개의 종류 위치가 다름)<br>
>터빈 : rpm(회전속도)<br>
>날씨 : °C(온도), %(습도), mm(강수량), m/s(풍속) 

 이러한 총 17개의 센서를 구축하게 됩니다. 이를 각각의 VM을 통해 구성하고 이를 Master VM을 통해 각각의 VM을 관리하게 될 것입니다.
 
 ![Untitled](/assets/images/2024-03-03/vm-arch1.png)

 * 온프레미스 서버를 이용해 VM환경을 구축하자

 ![Untitled](/assets/images/2024-03-03/onpre-1.png)

 온프레미스 서버를 이용하여 VM을 구축하고 이를 활용할 계획입니다. 네트워크 연결을 위해 ip 고정과 같은 네트워크 설정과 호스트네임을 설정하고 호스트네임을 통해 각기 다르게 동작하는 방식으로 구현할 예정이고 복제가 쉽도록 모든 게 준비되어있는 VM을 만들예정입니다.

## 2. AWS-SDK를 이용한 aws-iot-core MQTT 통신연결

 * AWSIoTPythonSDK.MQTTLib 라이브러리를 사용해 MQTT 연결은 다음과 같습니다.

![Untitled](/assets/images/2024-03-01/nuclear.png)

실제 발전소의 데이터와 iot 센서들을 가져올 수 없기 때문에 저희는 가상의 환경을 만들고 직접 iot 센서들을 구축할 것입니다. iot 센서들은 다음과 같이 각기 다른 pipe 정보와 터빈 발전기 제어봉 데이터를 전송하게 되고 이에 관한 데이터는 발전요구량을 토대로 긴밀하게 연관되어 있는 데이터를 구성해 보내게 될 것입니다.

정리하자면
>발전요구량 : MWh
>
>발전량 : MWh
>
>제어봉 높이 : m
>
>파이프 : MPa(압력), °C(온도) / (총 7개의 종류 위치가 다름)
>
>터빈 : rpm(회전속도)
>
>날씨 : °C(온도), %(습도), mm(강수량), m/s(풍속) 

이런식으로 구성된다. 이러한 총 17개의 각기 다른 센서 데이터를 aws iot core로 전송하게 된다.

>[다음포스트](https://code-y-learner.github.io/python/2024/02/13/static_data_VS_Dynamic_data.html)에서 더 정확한 구현방법과 연결 방법을 설명하겠습니다.

## 데이터 스트림 RAW DATA 저장
iot core에서 모인 모든 센서 데이터들은 data kinesis를 통해 전달되고 firehose에서 aws glue 데이터 스키마 테이블을 통해 s3에 전달되게 됩니다.
![kinesis](/assets/images/2024-03-01/kinesis.png) 
이러한 과정을 쉽게 설명하자면 kinesis는 통로 firehose는 s3에 직접 데이터를 보내는 과정 그리고 데이터의 스키마 정의는 데이터의 모양새를 정의해주고 데이터를 모아 5분에 한번 raw data 상태로 s3에 보관하게 됩니다.

>[다음포스트](https://code-y-learner.github.io/python/2024/02/13/static_data_VS_Dynamic_data.html)에서 더 정확한 구현방법과 상세 기능을 설명하겠습니다.

## 데이터 전처리 및 가공
raw data를 저장하는 s3 버킷에서 이제 데이터를 가공해 관리자 및 사용자들이 모니터링할 사이트까지 연결해야 합니다.
aws Athena, Lambda를 통해 데이터를 가공하여 Dynamo DB로 데이터를 저장합니다.

## 데이터 시각화
aws cloudwatch를 통해 데이터를 시각화합니다. 이를 사용자 및 관리자에게 직접적으로 공유를 할 수 있습니다.

## 경보 알림
센서의 오작동과 실제 비상상황을 구성하여 비상상황시 aws sns와 ec2의 웹서비스를 통해 휴대폰으로도 알림을 오도록 구성하였습니다.

## 전체적인 아키텍쳐

![architecture](/assets/images/2024-03-01/arch1.png)

> 첫번째 구상한 아키텍쳐입니다. 실제로 구상한 아키텍쳐는 좀더 다양한 기능을 추가하였습니다. [다음포스트](https://code-y-learner.github.io/python/2024/02/13/static_data_VS_Dynamic_data.html)에서 확인할 수 있습니다.