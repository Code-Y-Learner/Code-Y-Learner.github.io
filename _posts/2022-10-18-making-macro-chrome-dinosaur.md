---
layout: post
title: 크롬 공룡 게임 매크로로 알아보는 매크로
subtitle: pyautogui 와 pillow를 활용해보자
categories: MACRO
description: Making Chrome dinosaur-game macro
tags: pyautogui python macro pillow
---

### 크롬 게임 매크로로 알아보는 매크로
![macro_gif](https://elliethe.sirv.com/Images/Dinosaur.gif)

가끔 볼 수 있는 이 깜찍한 공룡게임을 아시나요? 갓겜의 3요소 공룡 가시 점프를 다 가진 게임인데 Chrome에 온라인 연결이 끊기면 이 공룡을 플레이하면서 온라인 연결이 되기를 기다리기도 합니다

오늘은 python에서 pyautogui 와 pillow를 활용한 자동플레이를 한번 해보겠습니다.

```python
import pyautogui  # pip install pyautogui
from PIL import Image, ImageGrab  # pip install pillow
import pygetwindow as gw
import time
```
pyautogui 키보드나 마우스 제어를 코드를 통해 가능하게 해줍니다. 그외에도 매크로 기능들이 많이 있습니다.

pillow PIL은 화면을 간다히 캡쳐해서 이미지 인식용으로 넣었습니다.

pygetwindow 매크로에서 나름 중요합니다. 매크로가 실행될 창을 고정시켜주거나 좌표를 사용하기에 편리합니다.

time 모듈 이용시 코드 사이에 일정기간 쉬는 시간을 줄 때 사용합니다.

소개해드린 모든 모듈은 소개해드린 것 이외에도 더 기능이 많습니다.

```python
def press(key):
    pyautogui.keyDown(key) #key 누르기
    time.sleep(0.03)
    pyautogui.keyUp(key) # key 떼기
    return
```
앞서 말한 pyautogui의 제어 부분 여기서는 key 값을 받으면 해당 인자를 누르고 0.03초뒤에 다시 키를 올립니다.

즉 키보드에서 아주 짧게 누른것을 의미합니다.

```python
image = ImageGrab.grab(bbox=(win.left, win.top, win.right, win.bottom)).convert('L')
data = image.load()
win = gw.getWindowsWithTitle('Chrome')[0]
win.activate()
def is_collision(data):
    # Check colison for birds
    for i in range(750, 790):
        for j in range(300, 325):
            if data[i, j] < 171:
                press("down")
                return
    # Check colison for cactus
    for i in range(750, 840):  # 750
        for j in range(335, 360):
            if data[i, j] < 100:
                press("up")
                return
    return
```
ImageGrab.grab은 현재 화면을 실시간으로 캡쳐합니다. bbox는 영역을 의미합니다. convert('L')은 흑백 느낌으로 캡쳐한다는 뜻입니다. Default값은 컬러입니다.

pygetwindow 는 현재 "Chrome"이라는 창의 첫번째를 잡고 activate 활성화 시킵니다.

bbox에 들어가는 win.left,win.top ...은 pygetwindow로 잡은 창의 영역을 의미합니다.

is_collision은 지금 for문으로 x좌표 750에서 690 y좌표 300에서 325까지 영역에서 픽셀값 하나라도 171보다 낮은 값이 들어오는지 검사합니다.

밑에도 마찬가지이고 아래와 위를 해놓은 이유는 아래에서는 장애물이 위네는 새가 날라와 숙여서 피해야 하기 때문입니다.



충돌판정을 알아보기
![collision_image](https://elliethe.sirv.com/Images/dinosour1.png)

지금 이미지에서 검은색과 회색으로 쳐놓은 부분이 is_collision의 영역이고 여기에 장애물이 오면 픽셀값이 조건식에 걸리게 되면서 공룡이 점프나 숙이기를 합니다.

```python
import pyautogui  # pip install pyautogui
from PIL import Image, ImageGrab  # pip install pillow
import pygetwindow as gw
import time


def press(key):
    pyautogui.keyDown(key)
    time.sleep(0.03)
    pyautogui.keyUp(key)
    return


def is_collision(data):
    # Check colison for birds
    for i in range(750, 790):
        for j in range(300, 325):
            if data[i, j] < 171:
                press("down")
                return
    # Check colison for cactus
    for i in range(750, 840):  # 750
        for j in range(335, 360):
            if data[i, j] < 100:
                press("up")
                return
    return


if __name__ == "__main__":
    win = gw.getWindowsWithTitle('Chrome')[0]
    win.activate()
    time.sleep(0.8)
    pyautogui.click()
    time.sleep(1)
    press('up')
    press('up')

    while True:
        image = ImageGrab.grab(bbox=(win.left, win.top, win.right, win.bottom)).convert('L')
        data = image.load()
        is_collision(data)
```
해당 로직의 모든 코드입니다. ![github]() 에도 올려져있습니다.

다음시간에는 매크로 심화 과정 게임 매크로에 대해 알아보겠습니다. 이 과정을 손보면 손쉽게 따라할 수 있습니다.