---
layout: post
title: 블로그개시
subtitle: 첫번째 게시글
categories: BLOG
tags: blog flask
description: First post in code-y-learner's blog
---

### 블로그 개시
![birthday_image](https://media.giphy.com/media/bnhAsd06orx3EkUJ8J/giphy.gif)

이 블로그는 Code-Y-Learner의 It 일상다반사를 기록하는 공간입니다.

일전에 Django를 통해 만든 웹사이트를 집에서 잠깐 배포 해본적이 있었는데 서버를 항상 유지할 수 없기 때문에 이번에는 flask로 RESTFUL-api 블로그를 설계하여 배포한 뒤 나의 IT 일상들을 기록하려고 하였다.

문제는 직접 배포를 할 수 없으니 배포를 대신 해주는 유무료 서비스들을 이용해야 하는데 이때 고른 해결책이 heroku였다

그렇다면 왜 이 블로그는 헤로쿠가 아니고 github블로그인가 이것에 대해 먼저 얘기해보고자 한다.



![blog_image](/assets/images/2022-10-05-firstpost/heroku_blog.PNG "blog_image_file")

나의 헤로쿠사이트는 <https://codeylearnerblog.herokuapp.com>는 여기서 확인할 수 있다.

헤로쿠를 결과적으로 이용하지 않는 가장 큰 이유는 헤로쿠의 서버였다. 서버가 미국에 있고 일정시간 신호가 없으면 서버가 다운되는데 이걸 복구할때 서버가 없다고 표시된다.

서버가 일정시간 반응이 없으면 절전모드처럼 서버가 다운되고 이를 다시 깨울려면 새로고침을 연타해야 된다는 것이다.

아무래도 무료 서비스이니 그럴 수있지만 느리기도 많이 느리고 나 이외의 사용자가 서버가 꺼진 상태에서 맞딱드릴 경우의 수와 무료 서비스는 서버가 일정사용량을 초과하면 이 역시도 한달간 서버가 다운된다는 점에서 결과적으로 github블로그를 개시하기로 하였다.


헤로쿠 사이트 개설과 flask를 통해 RESTFUL블로그를 만든 과정이 필요하다면 복습 차원에서 따로 다루기로 하겠다. 일단 내 깃허브에 해당 코드도 있고 헤로쿠를 이용할 시 생기는 여러가지 버그들을 해결하는 방법 또한 기록할 필요도 있기 때문이다.
