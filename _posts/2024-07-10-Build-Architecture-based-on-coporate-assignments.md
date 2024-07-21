---
layout: post
title: 과제전형 아키텍쳐를 실제 설계해보자
subtitle: 기업이 주는 과제를 AWS 환경에서 실제로 설계해보자
categories: AWS
description: 실제 과제전형에서 받았던 질문을 바탕으로 아키텍쳐를 설계하고 도식화 해보자(Let's design and diagram the architecture based on questions received in actual corporate assignments)
tags: Interview AWS
---

### 기업 과제


#### 1. 2,000명 이하의 동접수를 지원하는 채팅 서비스의 System을 AWS 서비스를 이용해서 구성해보십시오.( 요구사항 spring 기반 백엔드, vueJS 기반 프론트, 대용량 멀티미디어 파일 메시지 가능, 한달 후 장기보관)
![Archi1](/assets/images/2024-07-10/Archi1.png)

첫번째 아키텍쳐입니다. 비용 최적화를 우선으로 생각한 아키텍쳐입니다. 사용자는 CloudFront를 통하여 vueJS로 빌드된 S3 정적 호스팅 웹에 접근할 수 있고 2개의 가용영역으로 이루어진 Private Subnet에 있는 백엔드 서버와 교신합니다. 백엔드 서버는 spring boot나 kotlin으로 이루어져있고 EC2나 Fargate 중에 요구사항에 적절하면서 비용을 최적화한 모델을 선택합니다. 저는 
EC2보다 MSA환경에 적합하고 2000명 이하의 동접자라면 사용량에 따른 비용을 지불하는 Fargate가 더 적절하다고 생각하여 이렇게 구성하였습니다. 이러한 백엔드 서버의 CI/CD는 깃허브액션을 예를 들었지만 젠킨스 등 다양한 옵션을 선택할 수 있고 저는 Fargate를 예로 들었기때문에 ECR에 컨테이너 이미지를 저장 ECS를 통해 배포하는 예시를 들었습니다. S3에 수명주기 정책을 걸어 미디어 오브젝트에 대한 관리를 직접 지정할 수 있고 log 데이터 등은 다른 S3에서 다른 수명주기를 걸어 관리할 수 있습니다. 마지막으로 관리자는 public의 Bastion Host를 통해 
private 서브넷의 서버에 접속해 관리할 수 있으며 CloudWatch를 통해 긴급상황시 SNS를 통해 이메일 연락을 받도록 설계할 수 있습니다.

<br><br><br><br><br><br>

![Archi1](/assets/images/2024-07-10/Archi2.png)

두번째 아키텍쳐입니다. 첫번째와 가장 큰 차이점은 S3 정적호스팅을 Fargate로 프론트를 구성하였다는 것입니다. 이렇게 구성한 이유는 S3의 정적호스팅은 높은 트래픽을 처리할 수 있고 비용 효율성, 관리가 용이하지만 보안 문제를 해결하기 위해서는 빡센 OAI OAC 설정과 클라우드 프론트가 필수입니다. 보안에서의 핵심은 S3의 엔드포인트가 노출되거나 S3가 퍼블릭에 노출되지 않도록 하는 것입니다. 동적 컨텐츠를 제공하기 위해 같은 서브넷에 위치하면 S3에서 vpc endpoint를 통한것보다 짧은 네트워크 지연시간을 가집니다. 따라서 설정이 간편하고 짧은 네트워크 지연시간을 원한다면 2번째 구조가 더 적합하다고 생각합니다.

나머지 과제들과 그에 따른 답변들은 나중에 더 확실하게 적어놓도록 하겠습니다.