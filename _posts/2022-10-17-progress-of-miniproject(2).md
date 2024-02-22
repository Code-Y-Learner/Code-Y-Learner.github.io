---
layout: post
title: MiniProject 중간과정(2)
subtitle: 대시보드 flask web GUI와 연동
categories: PYTHON
description: progress of my miniproject(2) 
tags: flask web python GUI miniproject API
---

### GUI Todolist 추가
![pomodoro_image](https://elliethe.sirv.com/Images/pomodoro.PNG)

일단 기존의 Pomodoro 타이머에서 todolist 버튼을 추가하고 클릭시 Todolist창이 뜨도록하였다.

![todolist_image](https://elliethe.sirv.com/Images/pomodoro_todolist.PNG)

Todolist창은 기본적으로 Add_button과 Del_button을 통해서 추가 및 삭제를 할 수 있다.
Todolist는 기본적으로 항목과 오른쪽 콤보박스를 통해 "O" - 완료 "X" - 아직 미착수 "△" - 완성 중간단계 이런식으로 구분해서 항목이 추가될때마다 버튼이 하나씩 생긴다.

### API 동작 웹과 연동
기본적으로 api 동작은 모든 준비를 마쳤다. 포모도로 타이머의 모든 동작은 서버에 전송된다.

그리고 todolist도 연동에 성공해서 데이터베이스에 Todolist창 활성화시 자동적으로 서버 데이터 베이스의 todolist항목을 불러와 항목에 추가해준다
그리고 todolist에 해로운 항목을 Add 후 upload하면 새로운 항목만 데이터베이스에 추가된다.

아직 웹에서 api로 항목 삭제 및 수정 요청을 하는 것이 남아있다.

### 웹 UI

![image_entire_web](/assets/images/2022-10-14-progress-of-miniproject/flask_자기개발웹%20예시.PNG)

아직 웹의 UI는 그대로이다. 일단 API와 데이터베이스가 정리되야 웹에 로그인시 웹의 대시보드로 사용자에 맞춰 데이터를 띄워줄 수 있기 때문이다.

API와 데이터베이스는 거의 90% 이상 완성해서 곧 웹 UI 고치는 것과 GUI todolist창도 약간 더 직관적이고 깔끔하게 다듬을 예정이다.


