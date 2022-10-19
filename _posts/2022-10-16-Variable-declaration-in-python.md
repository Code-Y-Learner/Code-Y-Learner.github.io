---
layout: post
title: Python 변수 선언 총정리
subtitle: 주로 for loop 에서 변수 선언
categories: python
description: Declare variables in python in variable situations 
tags: python variable
---

## 변수 선언 총정리
변수를 선언할때 보통의 경우에는 고민할 점을 느끼지 않는다.

하지만 GUI를 설계하거나 데이터과학에서 변수를 여러개 선언해야 할 경우를 고민이 되는데 이 때 다양한 방법이 있습니다.
```python
# 1부터 9까지 column을 만들때
for i in range(1,10):
    globals()[f"column_{i}"] = i
```
이렇게 하면 globals 전역변수 column_1~ 9까지가 생성된다.
```python
# 지역변수
for i in range(1,10):
    locals()[f"column_{i}"] = i
```
지역변수가 필요할때는 이렇게 하면 된다.

문제는 class 안에서 클래스 하위의 self.variable을 정의할때는 이러한 지역 전역 변수 선언은 문제가 될 수 있다.

그럴땐 이렇게 할 수 있다.
```python
# 클래스 안에서 정의
class A():
    def __init__(self):
        for i in range(1,11):
            setattr(self, f"column_{i}" , i)
```
이렇게 하면 self.column_들이 정의되고 A.column_1 ~ 10 까지가 깔끔하게 선언할 수가 있다.

그런데 이런 변수 선언을 반대로 변수를 불러올때는 어떻게 해야할까?

물론 A.column_1, A.column_2, ... 이런식으로 하나하나 다 불러올 수 있지만 그것보다
```python
class A():
    def __init__(self):
        for i in range(1,11):
            setattr(self, f"column_{i}" , i)

    def check(self):
        for i in range(1,11):
            if getattr(self,f"column_{i}") == i:
                print('Yes {i} setattr, getattr works')

function = A()
function.check()
```
getattr()을 써서 다음과 같이 for loop 등에서 변수를 불러와 쓸 수 있다.
function.check()의 결과가 10번 다 잘 나오는 것을 확인할 수 가 있다.

마지막으로 하나 알아두자 lambda 안에서는 변수선언이 마지막으로 고정된다. 예를 들어
```python
for button in [self.button1,self.button2,self.button3]:
    button.clicked.connect(lambda: self.click(getattr(self,f"{button}"))
```
예를 들어 pyqt에서 다음과 같이 버튼을 누를때 click이라는 함수로 자기 자신을 인수로 가지는 코드를 설게할 경우

lambda에서는 변수선언이 for loop 마지막으로 고정된다. 따라서 인수로는 self.button3 만 들어가게 되는데

이는 다음과 같이 고칠 수 있다.
````python
for button in [self.button1,self.button2,self.button3]:
    button.clicked.connect(lambda *args, b=button : self.clicker(b))
````
lambda에게 직접 인수를 넘겨줘야 한다. lambda : 뒤에서의 변수선언을 조심하고 

b=button 또는 b=getattr(self,f"{button}") 이런식으로 직접 인수를 지정해주자

exec()를 이용한 방법들도 많은데 이는 다음에 또 알아보자

![good_image](https://media.giphy.com/media/tIeCLkB8geYtW/giphy.gif)