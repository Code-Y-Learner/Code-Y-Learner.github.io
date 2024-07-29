---
layout: post
title: 과제전형 문제들 나머지들 나의 답변들
subtitle: 기업이 주는 과제를 AWS 환경에서 실제로 설계해보자
categories: AWS
description: 실제 과제전형에서 받았던 질문을 바탕으로 아키텍쳐를 설계하고 도식화 해보자 2편(Let's design and diagram the architecture based on questions received in actual corporate assignments 2)
tags: Interview AWS
---

### 기업 과제


#### 2. Quiz 2 AWS에서 운용되는 솔루션의 비용 절감을 위한 활동과 방법에 대해서 자유롭게 기술해주십시오.

제가 자주 비용절감을 위해 이용한 방법은 컨테이너를 통한 Fargate MSA 설계입니다. 컨테이너를 이용하면 스케일 아웃 스케일 인이 게스트 OS의 부팅시간을 기다려야 하는 EC2 등 보다 더 빨라 가변적인 수요량에 대처가 빠르고 MSA 설계를 통해 가용성 있는 환경을 제공할 때 시간과금과 인스턴스당 가격을 부과하는 EC2보다 사용량에 따른 과금을 부과하는 Fargate등이 가용성도
높고 비용 절감에 도움이 되었습니다.
또한 스트리밍 사이트를 설계할때는 리전별 가격정책이 다른 것을 확인하여 비교적 빠르면서 가격을 낮추기 위해 도쿄리전을 활용하기도하고 DB를 구성할 때 NoSQL로 해결가능한 부분은 DynamoDB와 람다로 구성해 비용을 많이 아낄 수 있는 부분이 많았습니다. 특히 프로젝트에서
람다를 적극 활용하여 비용을 아낄 수 있는 부분이 많았습니다.

가용성과 보안적인 요구사항을 만족하면서 비용효율적 아키텍쳐를 설계한 경험과 함께 실제로 비용이 발생하면 예상과 다른 부분에서 더 많은 요금이 부과될 수 있는데 이를 어떻게 해결하였는지 등의 경험을 말하면 좋은 거 같습니다. RDS의 속도를 보조하기 위해 redis를 사용할때 생각보다 많은 비용이 발생해서 redis 서버의 인스턴스 유형 등을 변경하거나 RDS를 DynamoDB와 같은 비관계형 서비스를 함께 사용해 속도를 보조하는 방법 등이 있을 거 같습니다.

#### Quiz 3 시스템 모니터링 경험에 대해 기술해주십시오. 어떤 도구 (툴, 서비스, 등)를 어떤 목적으로 어떻게 사용했는지 기술해주십시오.

원전의 센서값을 통한 관리형 서비스를 AWS IotCore를 통해 구축할 때의 경험입니다. 센서의 값을 모니터링하고 센서의 값에 따라 관리자에게 SNS를 통해 알림을 주는 등 모니터링을 설계하였습니다. 관리자는 Iot SiteWise를 통해 실시간 센서값을 목적에 맞게 모니터링하고 CloudWatch를 통해 정해진 센서값에 대한 이상 감지시 SNS를 통해 알림을 주도록 설계하였습니다. 또한 온프레
미스 환경에서는 AWS Iot Core로 원전의 센서값을 보내는 가상의 VM 17개를 설계하였는데 마스터 VM에서 나머지 VM들이 보내는 센서값을 조절하는 웹관리콘솔페이지를 만들면서 나머지 VM들의 상태 cpu사용량, net 사용량 등을 모니터링 할 수 있는 오픈소스 netdata를 사용하여 모니터링을 한 적이 있습니다.

APM(Application Performance Monitoring)은 기본적으로 Cloud Watch를 사용할 수 있습니다. AWS 인스턴스들에 대한 것들이라면 Cloud Watch로 볼 수 있지만 대시보드를 구성하고 Cloud Watch의 반응성을 빠르게 할려면 비용이 부과될 수 있습니다. 사용하는 서비스에 따라 Cloud Watch 외에도 모니터링을 실시간 하거나 대시보드를 만드는 IOT Sitewise 같은 서비스등도 많고 직접 구축할 수도 있습니다.

#### Quiz 4 ALB, EC2, RDS 리소스를 테라폼 코드를 통해 형상관리하고자 합니다. 각 서비스를 테라폼 Terraform 코드로 구현해 주세요. 필수 조건 ALB : rule 80, 443 EC2 : Amazon Linux2, SSH 통신을 이용한 터미널 접속이 가능해야 합니다. RDS : MariaDB, EC2 가 인바운드 통신 가능해야 합니다.

```python
##########
# 서울리전
##########
provider "aws" {
region = "ap-northeast-2"
}
###########
#AWS key pair EC2 만들때 AWS 로그인
###########
resource "aws_key_pair" "deployer" {
key_name = "ec2-deployer-key"
public_key = # file("~/.ssh/id_rsa.pub") or "ssh-rsa
AAAAB3NzaC1yc2EAAAADAQABAAABAQD3F6tyPEFEzV0LX3X8BsXdMsQz1x2cEikKDEY0aIj41qgxMCP/iteneqXSIFZBp5vizPvaoI
R3Um9xK7PGoW8giupGn+EPuxIA4cDM4vzOqOkiMPhz5XK0whEjkVzTo4+S0puvDZuwIsdiW9mxhJc7tgBNL0cYlWSYVkz4G/fslNfR
PW5mYAM49f4fhtxPb5ok4Q2Lg9dPKVHO/Bgeu5woMc7RY0p1ej6D4CKFE6lymSDJpW0YHX/wqE9+cfEauh7xZcG0q9t2ta6F6fmX0a
gvpFyZo8aFbXeUBr7osSCJNgvavWbM/06niWrOvYX2xwWdhXmXSrbX8ZbabVohBK41 email@example.com"
}
############
#Security group(ssh 연결)
############
resource "aws_security_group" "allow_ssh" {
name =
"allow_ssh"
description = "Allow SSH inbound traffic"
ingress {
from_port = 22
to_port = 22
protocol = "tcp"
cidr_blocks = ["0.0.0.0/0"]
}
egress {
from_port = 0
to_port = 0
protocol =
"
-1"
cidr_blocks = ["0.0.0.0/0"]
}
}
#Security group(web)
resource "aws_security_group" "allow_web" {
name = "allow_web"
description = "Allow Web inbound traffic"
vpc_id = aws_vpc.main.id
ingress {
from_port = 80
to_port = 80
protocol = "tcp"
cidr_blocks = ["0.0.0.0/0"]
}
ingress {
from_port = 443
to_port = 443
protocol = "tcp"
cidr_blocks = ["0.0.0.0/0"]
}
egress {
from_port = 0
to_port = 0
protocol =
"
-1"
cidr_blocks = ["0.0.0.0/0"]
}
}
#Security group(db)
resource "aws_security_group" "allow_db" {
name = "allow_db"
description = "Allow DB inbound traffic"
vpc_id = aws_vpc.main.id
ingress {
from_port = 3306
to_port = 3306
protocol = "tcp"
cidr_blocks = [aws_instance.chatserver.private_ip]
}
egress {
from_port = 0
to_port = 0
protocol =
"
-1"
cidr_blocks = ["0.0.0.0/0"]
}
}
#############
# EC2
#############
resource "aws_instance" "web" {
ami = "ami-0fd54cba47d6e98dc"
instance_type = "t3.micro"
key_name = aws_key_pair.deployer.key_name
security_groups = [aws_security_group.allow_ssh.name]
tags = {
Name = "ChatServer"
}
}
##############
# ALB
##############
resource "aws_lb" "lb_chatserver" {
name = "load-balancer-chatserver"
internal = false
load_balancer_type = "application"
security_groups = [aws_security_group.allow_web.id]
subnets = [aws_subnet.public1.id, aws_subnet.public2.id]
enable_deletion_protection = false
}
##############
# ALB http, https 리스너
##############
resource "aws_lb_listener" "http" {
load_balancer_arn = aws_lb.lb_chatserver.arn
port = "80"
protocol = "HTTP"
default_action {
type = "forward"
target_group_arn = aws_lb_target_group.chatserver.arn
}
}
resource "aws_lb_listener" "https" {
load_balancer_arn = aws_lb.lb_chatserver.arn
port = "443"
protocol = "HTTPS"
default_action {
type = "forward"
target_group_arn = aws_lb_target_group.chatserver.arn
}
}
##############
# RDS maria_db
##############
resource "aws_db_instance" "chatdb" {
allocated_storage = 400
storage_type = "gp3"
engine = "mariadb"
engine_version = "10.11.6"
instance_class = "db.t3.micro"
name = "chatdb"
username = "admin"
password = "password"
parameter_group_name = "default.mariadb10.11.6"
vpc_security_group_ids = [aws_security_group.allow_web.id]
tags = {
Name =
"ChatDBInstance"
```
