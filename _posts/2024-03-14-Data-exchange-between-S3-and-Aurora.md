---
layout: post
title: S3, Aurora 사이의 데이터 교환
subtitle: s3 데이터를 데이터베이스에서 활용하자
categories: AWS
description: S3와 AWS Aurora MySQL 간 데이터 주고받기 (업로드 & 다운로드) Data exchange between S3 and Aurora
tags: aws s3 aurora
---

# S3와 AWS Aurora MySQL 간 데이터 교환 필요성

>만약 [S3 Aurora DB 데이터 교환 방법](https://code-y-learner.github.io/python/2024/02/13/static_data_VS_Dynamic_data.html)이 궁금하시다면 바로 다음 포스트로 넘어가시면 됩니다.

## S3의 중요성

S3는 aws 스토리지 서비스 중 하나이면서 aws 서비스 전체 중에서도 가장 많이 쓰는 서비스 중 하나로 단순한 저장소로도 쓸 수 있지만 다른 aws 서비스와 연결되어 확장성이 가장 중요한 특징입니다. 예를 들어 웹 프론트 파일을 업로드하여 정적 웹 호스팅에 쓰여지거나 그 자체로 DB처럼 쓰이거나 각종 미디어 파일 변환등의 저장소로 활용되기도 합니다.

## CSV파일을 Aurora RDS에 넣어보자

일전에 [상권분석_프로젝트](https://code-y-learner.github.io/python/2024/02/13/static_data_VS_Dynamic_data.html)에서 상권 공공데이터를 통해 관계형 DB를 설계한 적이 있었는데 python-mysql를 활용하여 auroraDB 엔드포인트에 연결하여 직접 csv파일을 임포트하여 하나하나 넣어준 적이 있습니다.

```python
import pandas as pd
import mysql.connector

# Connect to Aurora MySQL database
def connect_to_database(host, user, password, database):
    try:
        conn = mysql.connector.connect(host=host, user=user, password=password, database=database)
        if conn.is_connected():
            print('Connected to MySQL database')
            return conn
    except mysql.connector.Error as e:
        print(f'Error connecting to MySQL: {e}')
        return None

# Load CSV file into a Pandas DataFrame
def load_csv(file_path):
    try:
        df = pd.read_csv(file_path)
        return df
    except Exception as e:
        print(f'Error loading CSV file: {e}')
        return None

# Insert DataFrame data into MySQL database
def insert_data(conn, df, table_name):
    try:
        cursor = conn.cursor()
        for index, row in df.iterrows():
            values = tuple(row)
            # print(values[1:])
            query = f"INSERT INTO cd_area(cd_div_cd, cd_cd, br_cd, ab_cd, area, lon, lat) VALUES {values[1:]}"
            cursor.execute(query)
        conn.commit()
        print('Data inserted successfully')
    except Exception as e:
        print(f'Error inserting data into MySQL: {e}')
        conn.rollback()

# 데이터베이스 생성
def create_database(host, user, password, database_name):
    try:
        conn = mysql.connector.connect(host=host, user=user, password=password)
        cursor = conn.cursor()

        # Create the database
        cursor.execute(f"CREATE DATABASE {database_name}")

        print(f"Database '{database_name}' created successfully")

        cursor.close()
        conn.close()
    except mysql.connector.Error as err:
        print(f"Error: {err}")

# 테이블 생성
def create_table_from_csv(host, user, password, database, table_name, csv_file):
    try:
        # Load CSV file into a DataFrame
        df = pd.read_csv(csv_file)

        # Connect to the database
        conn = mysql.connector.connect(host=host, user=user, password=password, database=database)
        cursor = conn.cursor()

        # Infer table schema from the CSV file columns
        columns = ", ".join([f"{col} VARCHAR(255)" for col in df.columns])

        # Create the table
        create_table_query = """
            DROP TABLE {};
            
            CREATE TABLE IF NOT EXISTS {} (
                cd_div_cd VARCHAR(255) NOT NULL,
                cd_cd INT PRIMARY KEY,
                br_cd INT NOT NULL,
                ab_cd INT NOT NULL,
                area INT NOT NULL,
                lon DOUBLE NOT NULL,
                lat DOUBLE NOT NULL
            )
        """.format(table_name, table_name)

        cursor.execute(create_table_query)

        print(f"Table '{table_name}' created successfully")
        conn.commit()
        cursor.close()
        conn.close()
    except Exception as e:
        print(f"Error: {e}")

# Main function to execute the process
def main():
    # Database connection details
    host = <endpoint Of your RDS>
    user = <ID>
    password = <PW>
    database = <your DB name>

    # CSV file path
    csv_file = <your csv 파일 경로>

    # Table name in MySQL
    table_name = 'cd_area'

    create_database(host, user, password, database)
    create_table_from_csv(host, user, password, database, table_name, csv_file)
    # Connect to MySQL database
    conn = connect_to_database(host, user, password, database)
    if conn:
        # Load CSV file into DataFrame
        df = load_csv(csv_file)
        if df is not None:
            # Insert data into MySQL
            insert_data(conn, df, table_name)
            conn.close()
        else:
            print('Exiting due to errors in CSV file loading')
    else:
        print('Exiting due to database connection errors')

if __name__ == '__main__':
    main()
```

 하지만 데이터가 5년치 분량이 되가면서 행의 개수가 140만개 컬럼의 55개가 되면서 많은 데이터를 직접 쓸려고 하니 데이터 서버에 많은 쓰기 부하와 비용도 발생하였습니다.

 이런 문제를 극복하기 위해서 2가지 방법이 있는데
 > 1. insert query를 여러행을 삽입해 시간을 n배로 줄인다.
 > 2. S3에 data를 RDS로 import 한다.

2가지 해결방법을 모두 시도했는데 2번이 압도적으로 경제적 시간비용적으로 효율적이었습니다. 
> 1번의 방법은 rds 부하도 크고 100만행이 넘어가면 시간도 약 몇시간을 소요
>
>2번 방법은 100만행의 자료삽입도 몇초이내에 가능 

[다음포스트](https://code-y-learner.github.io/aws/2024/03/17/Data-exchange-between-S3-and-Aurora(2).html)에서 이러한 S3의 데이터 마이그레이션 방법을 모든 시행착오와 함께 알려드리겠습니다.