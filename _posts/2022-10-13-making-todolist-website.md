---
layout: post
title: MiniProject 자기개발 웹 만들기
subtitle: 대시보드 flask web GUI와 연동
categories: PYTHON
description: Making my web site with RESTFUL API with GUI controller
tags: flask web python GUI miniproject API
---

### 1. Pomodoro Timer + Todolist GUI 만들기

### 2. GUI에서 웹으로 API post

### 3. Flask 웹 에서 해당 GUI의 기록 연동하기

### 3. Dashboard

일단은 이렇게 구상하고 있다 현재 자바를 배우고 있는데 이전에 지금까지 배운 것을 복습하면서 실제로 내가 자기개발을 하면서 이런 기능을 직접 만들어서 관리해보고 싶어서 만들어본다

구체적인 계획은 Pomodoro Timer를 클래스로 수정하고
flask로 웹사이트 만들어서 api 기능 구현 내가 pomodoro를 사용할때 생성되는 데이터를 웹사이트에 보내기

마지막으로 웹사이트에 로그인하면 내가 보낸 정보가 dashboard로 출력
거기에다가 todolist를 하나 더 추가할 것인데 pomodoro에서 버튼을 누르면 gui가 todolist로 바뀌고 todolist를 작성 및 편집하면 이를 웹사이트에 연동해 다시 dashboard에서 이를 확인할 수 있게 만들어볼 생각이다.