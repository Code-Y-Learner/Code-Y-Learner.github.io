---
layout: post
title: AWS를 활용한 실시간 스트리밍 사이트 구축(2)
subtitle: AWS-IVS등을 통하여 네이버 치지직 사이트를 구현해보자(2)
categories: AWS
description: AWS-IVS를 활용한 실시간 스트리밍 사이트 구축 네이버 치지직 사이트를 직접 구현해보자(Real-time streaming site using AWS-IVS)
tags: aws ivs miniproject
---

### 실시간 스트리밍 사이트 구축 프로젝트(2)

#### 전체 아키텍츠 구조

![bg](https://code-y-learner.github.io/portfolio/assets/img/p3-archi.png)

전반적인 아키텍쳐에 대한 자세한 설명은 설명은 [발표PPT](https://code-y-learner.github.io/aws-streamingProject)에서 확인 하실 수 있습니다.

세부적으로는 웹 및 API서버, IVS, 미디어 등으로 나누어집니다.

#### 웹 및 API 서버
![bg](https://raw.githubusercontent.com/Code-Y-Learner/aws-streamingProject/main/images/%EC%B5%9C%EC%A2%85%EC%88%98%EC%A0%95%EB%B3%B8-15.png)

웹 및 API서버는 모두 컨테이너로 ECS를 통해 배포하였고 이렇게 배포한 가장 큰 이유는 2가지입니다.

##### 첫번째 비용

EC2등을 이용할 경우 비용은 시간과금으로 프로젝트 계속해서 과금이 되기 때문에 수요가 가변적이고 그러면서도 가용성있는 환경을 제공하기에는 적합하지 않습니다. 특히 비용적인 측면에서 Fargate를 통한 ECS를 선택해 Lambda처럼 사용량에 따른 과금이 되도록 하였습니다. 프로젝트를 2주이상 진행하였기에 EC2에 비해 상당한 비용을 절감할 수 있었습니다.

##### 두번째 마이크로서비스

Cognito API, 미디어 API, 웹 서버를 분리해서 마이크로 서비스로 디자인하여서 안정적인 가용성있는 환경을 제공합니다. 각 컨테이너는 최소 2에서 최대 개수를 상황에 맞게 설정하여 높은 스트레스 환경에서도 대응 가능하도록 설정하였습니다. 특히 EC2로 디자인하였다면 비용을 위해 로드밸런싱 및 마이크로 서비스를 제공하려면 최소3개의 서버에서 가용성을 위해 6개 또 서버의 수용에 따라 로드밸런싱으로 늘어나는 것을 고려해본다면 이렇게 ECS를 통해 배포를 하여 가용성있는 마이크로서비스를 더욱 비용절감적으로 설계할 수 있었습니다.

#### 미디어 아키텍쳐

![bg](https://raw.githubusercontent.com/Code-Y-Learner/aws-streamingProject/main/images/%EC%B5%9C%EC%A2%85%EC%88%98%EC%A0%95%EB%B3%B8-16.png)

미디어 부분에 있어 S3를 활용한 백엔드 환경을 구성하였습니다. IVS를 통한 실시간 방송은 모두 자동적으로 다시보기 기능을 제공하도록 하였고 S3의 특정 해시값을 가진 폴더로 저장되는데 이를 이벤트 브릿지에서 받아 람다를 통해 해당 경로를 저장하였습니다. 이를 통해 RDS에서 스트리머별 다시보기 정보를 저장할 수 있었고 VOD 서비스의 경우 Media Convert 기능을 통해 m3u8 로 비디오 파일을 변화하여 언제든지 올린 VOD를 웹에서 해상도에 따른 시청환경을 제공하도록 하였습니다. 이 과정은 람다를 통해 진행되며 S3에 원본 파일 등 안정적인 파일관리를 하도록 하였습니다.


#### AI 파트
![bg](https://raw.githubusercontent.com/Code-Y-Learner/aws-streamingProject/main/images/%EC%B5%9C%EC%A2%85%EC%88%98%EC%A0%95%EB%B3%B8-18.png)

실시간 스트리밍 특성상 있을 수 있는 선정적이나 불건전한 스트리밍을 방지하기 위해 썸네일이 1분마다 갱신되는 S3의 경로를 잡아 Recogniton을 통해 태그를 검출할 수 있습니다. [앞선 포스트](https://code-y-learner.github.io/aws/2024/05/10/Build-real-time-streaming-site-using-AWS.html)에서 담배를 예시로 담배를 검출해내고 해당 유저를 관리자 권한에서 밴 하는 것을 보실 수 있습니다.

또한 SageMaker를 통해 AI 이미지 생성을 하여 스트리머용 방송배경화면을 생성하는 기능 또한 만들어보았습니다.

이것으로 아키텍쳐에 대한 전반적 설명을 마치겠습니다.

IVS를 활용하는 법(채널 생성, 채팅방 생성 어떻게 프론트 서버와 연결하는지 등등) EVENT Bridge를 통해 어떻게 랜덤 해시값의 경로를 찾는지 람다를 인프라에서 연결하는 방법등 아키텍쳐를 설계 구축하면서 트러블 슈팅 및 알게된 자세한 내용들을 깃허브를 통해 댓글을 달아주시면 아는 한에서 최대한 답변드리도록 하겠습니다.