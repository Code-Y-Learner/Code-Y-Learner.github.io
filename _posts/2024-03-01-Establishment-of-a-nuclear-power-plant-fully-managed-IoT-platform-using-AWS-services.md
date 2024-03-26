---
layout: post
title: aws 서비스를 활용한 완전관리형 iot 플랫폼 구축
subtitle: aws iot를 통한 원자력 발전소 완전관리형 플랫폼을 구축해보자
categories: AWS
description: Establishment of a nuclear power plant fully managed IoT platform using AWS services
tags: aws iot miniproject
---

# 공공기관 운영 클라우드화의 필요성

 국내 공공기관의 운영 및 데이터를 클라우드로 마이그레이션 하는 사례가 많아지고 있다. 주로 네이버를 필두로 국내에는 kt, nhn 등등 기업들이 클라우드 서비스를 제공하고 있고 국내 공공기관은 여러가지 보안과 국민 데이터를 관리하기 위해 국내 클라우드 서비스를 활용하려고 있습니다. [네이버, 원자력과 인공지능의 콜라보 기대(기사)](https://www.kaeri.re.kr/board/view?menuId=MENU00326&linkId=11151)를 보면 알 수 있듯이 발전소 분야에서도 데이터를 클라우드에 저장하고 이를 분석하는 기술도 클라우드에서 동시에 이루어지는 완전관리형 서비스를 구축이 점점 늘어나고 있다. 이는 원시 데이터를 클라우드와 이중화시켜 혹시라도 있을 사고와 데이터의 보관을 이중화해 고가용성을 대비하고 완전관리형 서비스를 통해 혹시라도 있을 사람의 실수로 인한 오작동을 미리 차단할 수 있는 장점이 있습니다.


## iot를 이용한 스마트 팩토리를 이용한 완전관리형 서비스 구축해보자

![Untitled](/assets/images/2024-03-01/digital.png)

이러한 공공기관의 운영을 스마트 팩토리의 핵심 기술 디지털 트윈, 보안, 사물인터넷 기술을 활용해 완전 관리형 서비스를 클라우드에 구축하는 것을 목표로 프로젝트를 만들어 진행하였습니다. 저희는 공공기관의 발전소 중 완전관리형 서비스를 통해 사람의 실수를 가장 줄일 수 있는 원자력 발전소를 기준으로 아키텍쳐를 만들었습니다.

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

>[다음포스트](https://code-y-learner.github.io/aws/2024/03/03/Establishment-of-a-nuclear-power-plant-fully-managed-IoT-platform-using-AWS-services(2).html)에서 더 정확한 구현방법과 연결 방법을 설명하겠습니다.

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