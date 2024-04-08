---
layout: post
title: S3, Aurora 사이의 데이터 교환(2)
subtitle: s3 데이터를 데이터베이스에서 활용하자
categories: AWS
description: S3와 AWS Aurora MySQL 간 데이터 주고받기 (업로드 & 다운로드) Data exchange between S3 and Aurora
tags: aws s3 aurora
---

# S3와 AWS Aurora MySQL 간 데이터 주고받기

## 1. IAM Role 생성

다음과 같은 IAM Role을 생성해야 합니다.

![IamRole](/assets/images/2024-03-17/iamRole1.png)

![IamRole2](/assets/images/2024-03-17/iamRole0.png)

 신뢰관계를 rds 에서 add role to database를 클릭한 뒤 AmazonS3FullAccess를 역할에 추가해주시면 됩니다.

그 후 arn을 복사해둡니다.

## AuroraDB 파라미터 그룹 변경

필요에 맞는 auroraDB(MySQL)을 직접 생성한 뒤 파라미터 그룹을 적용하여 S3에서 데이터 이관이 가능하도록 설정합니다.

Aurora MySQL 버전 3(MySQL 8)에서는 aws_default_s3_role만을 변경하면 됩니다. 파라미터 그룹을 생성하는 방법은 
![parameter](/assets/images/2024-03-17/Parameter0.png)

![parameter1](/assets/images/2024-03-17/Parameter1.png)

다음과 같이 MySQL 8버전을 선택하고 파라미터 그룹 이름을 정한 뒤 직접 클릭하여 파라미터 그룹의 파라미터를 직접 수정해야 합니다.

![parameter2](/assets/images/2024-03-17/Parameter2.png)

편집을 눌러 화면에 보이는 aws_default_s3_role을 변경해야 합니다. 파라미터를 직접 입력 후 비어있는 값에 아까 전 만들어두었던 IamRole의 ARN을 입력하여 줍니다.


이제 aurora 클러스터 DB에서 직접 파라미터 그룹을 변경할 차례입니다. auroraDB에서 수정을 누르고 다음과 같이 파라미터 그룹을 변경합니다.
![parameter3](/assets/images/2024-03-17/Parameter3.png)

> 이제 변경된 파라미터 그룹을 적용하기 위해 rds를 재부팅해야 합니다. (중요!)<br>
> 이때 RDS와 S3는 동일한 리전에 위치해야 합니다.

그 후에 직접적으로 txt csv 파일등을 테이블 형식에 맞춰 aurora DB에 직접 입력이 가능합니다.

```sql
LOAD DATA FROM S3 's3://dbbucket/customerdata.csv' 
    INTO TABLE <table-name>
    FIELDS TERMINATED BY ','
    LINES TERMINATED BY '\n'
    (ID, ... columns);
```

이 과정까지 시행착오가 좀 있었는데 일단 공식 홈페이지의 [메뉴얼](https://docs.aws.amazon.com/ko_kr/AmazonRDS/latest/AuroraUserGuide/AuroraMySQL.Integrating.LoadFromS3.html)이 좀 불친절했고 rds를 재부팅안하면 파라미터 그룹이 적용이 안되는 등 다양한 트러블을 만났고 이러한 일이 없게 다들 쉽게 s3에서 rds로 데이터를 이관하여 쉽고 빠른 데이터를 사용가능해지길 바라는 마음과 기록을 위해 작성하였습니다.

> 1. 파라미터 그룹 변경이 되었는지 확인 <br>
>   -> rds 재부팅 필요
> 2. s3와 rds가 같은 리전에 있는지 확인 <br>
>   -> s3의 엔드포인트가 필요한지 확인