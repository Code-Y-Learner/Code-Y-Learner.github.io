---
layout: post
title: Tic Tac Toe 마무리
subtitle: GUI로 tic tac toe
categories: PYTHON
description: Python TicTacToe game with PyQt GUI-version
tags: python GUI tictactoe pyqt
---

## Pyqt6로 tictactoe를 만들어보자
이 포스트는 [이전 글(tictactoe)](https://code-y-learner.github.io/python/2022/10/08/Python-tictactoe.html) 에서 이어집니다.

이전에 tictactoe를 GUI로 만든다고 하였는데 진행하고 있는 [project](https://code-y-learner.github.io/flask/2022/10/13/making-todolist-website.html) 와 관련해서 GUI를 tkinter에서 Pyqt로 갈아타면서 공부하기 괜찮은 주제인거 같아서 만들어봤습니다.

먼저 tkinter에서 pyqt로 갈아탄 이유는 tkinter는 너무 오래된 오픈소스라는 걸 알았고 이참에 하나 더 파이썬 GUI를 배워두는 편이 좋겠다고 생각해서이다.

```python
from PyQt6.QtWidgets import QLabel, QMainWindow, QPushButton, QApplication
from PyQt6 import uic


class UI(QMainWindow):
    def __init__(self):
        super(UI,self).__init__()

        #load the ui file
        uic.loadUi('toe.ui',self)
        self.setWindowTitle('Tic tac toe')

        self.counter = 0
        for i in range(1,11):
            setattr(self, f"button{i}" , self.findChild(QPushButton,f"pushButton_{i}"))
        self.button_list = [self.button1,self.button2,self.button3,self.button4,self.button5,self.button6,self.button7,self.button8,self.button9]
        self.label = self.findChild(QLabel,"label")
        for button in self.button_list:
            button.clicked.connect(lambda *args, b=button : self.clicker(b))
        self.button10.clicked.connect(self.reset)
        self.show()

    def clicker(self, button):
        if self.counter % 2 ==0:
            button.setText("X")
            self.label.setText("now 'O's turn")
        else:
            button.setText("O")
            self.label.setText("now 'X's turn")
        button.setEnabled(False)
        self.check_win()
        self.counter += 1
        if self.counter == 9:
            self.finish()
            self.label.setText("Tie try again")

    def reset(self):
        for button in self.button_list:
            button.setText("")
            button.setEnabled(True)
            button.setStyleSheet('QPushButton {color: #797979;}')
        self.counter = 0

    def check_win(self):
        #row win
        for i in range(1,10,3):
            if getattr(self,f"button{i}").text() !='' and getattr(self,f"button{i}").text() == getattr(self,f"button{i+1}").text() == getattr(self,f"button{i+2}").text():
                self.finish(getattr(self,f"button{i}"),getattr(self,f"button{i+1}"),getattr(self,f"button{i+2}"))
        # column win
        for i in range(1,4):
            if getattr(self,f"button{i}").text() !='' and getattr(self,f"button{i}").text() == getattr(self,f"button{i+3}").text() == getattr(self,f"button{i+6}").text():
                self.finish(getattr(self,f"button{i}"), getattr(self,f"button{i+3}"), getattr(self,f"button{i+6}"))
        # cross win
        if self.button1.text() != '' and self.button5.text() == self.button1.text() == self.button9.text():
            self.finish(self.button1,self.button5,self.button9)
        if self.button3.text() != '' and self.button3.text() == self.button5.text() == self.button7.text():
                self.finish(self.button3,self.button5,self.button7)
    def finish(self,*args):
        for button in args:
            button.setStyleSheet('QPushButton {color: red;}')
        for button in self.button_list:
            button.setEnabled(False)
        if self.counter %2 == 0:
            self.label.setText("'X' win")
        else:
            self.label.setText("'O' win")

app = QApplication([])
UIWindow = UI()
#Run the app
app.exec()
```
만들면서 느낀 점은 tkinter도 그리 나쁘지 않다고 느꼈습니다. GUI 구현이라는 주제에 대해서는 tkinter가 기능적으로 부족함이 있는 지는 아직 저에게 있어 보이지 않았습니다.

하지만 PyQt의 경우 UI를 외부에서 미리 짜서 load할 수 있다는 장점이 tkinter처럼 실행하면서 UI의 위치를 보고 조절하는 과정이 생략될 수 있다는 것과 대조적인 차이점이 있었습니다.

그리고 만들면서 정리해야 될 몇가지 점을 배우게 되었는데 다음포스트에서 다루겠습니다.



![gif_for_love](https://media.giphy.com/media/S9oNGC1E42VT2JRysv/giphy.gif)

