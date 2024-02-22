---
layout: post
title: 플라스크 웹개발 프로젝트
subtitle: 내가 만든 블로그 뜯어보기
categories: PYTHON
description: Flask web development by my site
tags: flask blog web
---

###  Flask
```python
from flask import Flask
```
Flask는 Python 마이크로 웹 프레임워크이다.

마이크로 웹 프레임워크란 말 그대로 소형화된 프레임워크로 필요한 기능만 딱딱 넣어 확장성에 장점이 있다.

따라서 나와 같이 블로그 등을 만들때 굳이 풀스택 프레임워크가 필요하지 않은 경우 더 유용할 뿐더러 필요한 부분은 직접 확장을 하면서 구현해나갈 수 있다. (필요한 기능을 하나씩 만들어나가는 재미가 있다.)

### Flask 기반 웹 환경 구축
```
c:\>python -m venv c:\path\to\myenv
```
윈도우 기준 cmd에서 파이썬으로 가상환경을 만들어 준다.

```
python3 -m venv /path/to/new/virtual/environment
```
리눅스는 이런식으로 가상환경을 구축할 수 있다.

가상환경 아래 본인의 목적에 맞게 폴더를 구성한 뒤 main.py를 생성한다.
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello World!'

if __name__ == '__main__':
    app.run()

```
헬로우 월드가 웹사이트를 통해 출력된다. 



### Flask 기능

이는 [첫번째포스트](https://code-y-learner.github.io/blog/2022/10/05/firstpost.html) 와 연결된 부분인데 내가 블로그에서 만든 기능은

![blog_image](/assets/images/2022-10-05-firstpost/heroku_blog.PNG "blog_image_file")

```python
from flask import Flask, render_template, redirect, url_for, flash, abort

```
template(html)을 불러와주는 render_template, 주소값에 해당되는 메서드와 연결되는 redirect
예를 들어 render_template은 html을 불러와 띄워주는 역할에 주로 쓰이고 redirect는 로그인 메서드가 있는 로그인 url에 주소를 할당해 메서드를 동작할 때 쓰입니다.

```python
from werkzeug.security import generate_password_hash, check_password_hash
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy.orm import relationship
from flask_gravatar import Gravatar
from flask_wtf import FlaskForm, CSRFProtect
from flask_bootstrap import Bootstrap
from flask_ckeditor import CKEditor
```
템플릿을 보여주는 부분을 당담하는 bootstrap
보안을 당담하는 flask_wtf의 CSRFProtect와 form을 생성하는 FlaskForm
데이터베이스는 sqlalchemy로 댓글기능은 gravatar와 글 작성을 도와주는 CKEditor(게시판 글쓰기)
비밀번호를 당담하는 hash함수 generate_password_hash
등으로 이루어져 있다.

자세한 기능 부분은 github에서도 확인이 가능하다.
기능 동작은 [나의 블로그](https://codeylearnerblog.herokuapp.com) 에서 확인할 수 있는데 내 계정으로 로그인하지 않는 이상 글을 쓰거나 편집할 수는 없고 댓글을 작성을 할 수가 있다.