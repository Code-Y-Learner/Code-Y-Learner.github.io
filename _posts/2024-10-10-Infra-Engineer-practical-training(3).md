---
layout: post
title: 인프라 엔지니어 실무교육 (3)
subtitle: 클라우드 컴퓨팅 아키텍쳐 분석
categories: Cloud
description: 클라우드 컴퓨팅 아키텍쳐 분석(Cloud computing architecture analysis)
tags: Cloud Infra
---

### 인프라 엔지니어 교육 3

[앞선 포스팅](https://code-y-learner.github.io/infra/2024/10/07/Infra-Engineer-practical-training(2).html)에서 이어서 S3와 오브젝트 스토리지를 설명하기 전에 클라우드 기술에 대해 전반적으로 모두 훑어서 핵심을 먼저 정리하면 S3 오브젝트 스토리지를 모두 다룰 수 있을 거 같아서 클라우드에 대해 먼저 다뤄보겠습니다.<br>실제 IT 신입사원면접에서 클라우드에 대해 설명해보라는 질문이 받은 적이 있는데 익숙하면서도 실제로 말로 표현하기 어려웠던 기억이 있습니다. 간략하면서도 핵심5가지를 짚어주면 클라우드에 대해 잘 표현할 수 있을것입니다.

#### 클라우드의 5가지 특징

클라우드 기술을 이해하기 위해서는 NIST(미국 국립표준기술원)에서 클라우드라 불리기 위한 5가지 필수적인 요소를 보면 그 특징과 등장배경을 알 수 있습니다.<br>**1. On-demand self-service**<br>**2. Broad network access**<br>**3. Resource pooling**<br>**4. Rapid elasticity**<br>**5. Measured service**


#### 1. 주문형 셀프서비스 (On-demand self-service)

클라우드 컴퓨팅의 첫 번째 핵심 요소는 사용자가 원하는 시점에 컴퓨팅 자원을 요청하고 사용할 수 있는 **주문형 셀프 서비스**입니다. 이는 사용자가 별도의 관리자 개입 없이, 웹 포털이나 API를 통해 즉시 필요한 리소스를 자동으로 프로비저닝할 수 있다는 점에서 큰 장점을 제공합니다. 이를 통해 클라우드 인프라는 보다 유연하고, 사용자는 빠르게 필요에 따라 확장 또는 축소할 수 있습니다.

#### 2. **광범위한 네트워크 접근성 (Broad network access)**
클라우드 자원은 **광범위한 네트워크 접근성**을 통해 어디서든지 인터넷을 통해 액세스 가능합니다. 이로 인해 다양한 장치(데스크탑, 모바일, 태블릿 등)에서 클라우드 서비스를 사용할 수 있게 됩니다. 네트워크를 통한 접근성은 클라우드 아키텍처 설계에서 중요한 부분으로, 데이터 전송 및 서비스 지연을 최소화하기 위해 CDN(Content Delivery Network)과 같은 네트워크 최적화 기술이 함께 고려됩니다.

#### 3. **리소스 공유 (Resource pooling)**
클라우드 서비스 제공자는 다중 사용자를 위해 자원을 **풀링**하여 여러 사용자 간에 동적으로 배분합니다. 이러한 **멀티 테넌트** 모델은 물리적 또는 가상 자원을 사용자 수요에 따라 할당하며, 사용자들은 각자 자신에게 할당된 자원만을 사용하는 것으로 보이지만, 실제로는 공유된 인프라를 사용하게 됩니다. 자원의 풀링은 효율성을 극대화하고 비용 절감을 가능하게 합니다.

#### 4. **빠른 확장성 (Rapid elasticity)**
**빠른 확장성**은 클라우드의 또 다른 중요한 특징입니다. 클라우드 서비스는 사용자의 수요 변화에 맞춰 자원을 빠르게 확장 또는 축소할 수 있는 유연성을 제공합니다. 이로 인해 트래픽 급증이나 변화에 대해 자동으로 대응할 수 있으며, 필요 시에는 리소스가 거의 무한정 제공되는 것처럼 보이게 됩니다. 이와 같은 확장성은 클라우드 네이티브 애플리케이션 설계 시 핵심 고려 사항으로, 마이크로서비스 아키텍처가 이에 대응할 수 있는 좋은 사례입니다.

#### 5. **측정가능한 서비스 (Measured service)**
클라우드 서비스는 **측정된 서비스**를 통해 자원의 사용량을 모니터링하고, 이에 따라 비용이 청구됩니다. 이는 클라우드 서비스의 투명성과 효율성을 높여주며, 사용자는 자신이 사용한 만큼만 비용을 지불하게 됩니다. 모니터링 시스템은 실시간으로 자원의 사용량을 추적하며, 이는 클라우드 서비스 제공자가 더 나은 자원 관리를 할 수 있도록 도와줍니다. 클라우드 아키텍처는 이러한 측정 시스템을 기반으로 하여 자원의 과도한 사용을 방지하고 효율적인 운영을 가능하게 합니다.

#### 결론

클라우드의 주요 특징을 통해 왜 서버를 빌리는 똑같은 방식의 호스팅서버와 클라우드는 다르게 표현되고 대부분의 공기업부터 사기업까지 클라우드로의 전환을 하는지 이 5가지 요소를 보면 확실히 알 수 있습니다. 지금은 이러한 단순한 클라우드 전환을 넘어 클라우드의 특징을 극대화하기 위해 클라우드 네이티브로 이미 넘어간 추세입니다.