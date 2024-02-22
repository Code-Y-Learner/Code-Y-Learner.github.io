---
layout: post
title: Python GUI 만들기
subtitle: tkinter를 통해 타이머를 만들어보자
categories: PYTHON
description: Making Pomodoro timer in python using tkinter
tags: python tkinter pomodoro miniproject
---

## 이번에 pomodoro 타이머를 만들어보자

Pomodoro는 일단 25분 집중 후 5분 휴식을 하는 것인데 이것을 통해 시간관리를 진행할때가 많다

갑자기 타이머를 만드는 이유는 
tictactoe를 GUI로 전환하기 전 GUI에 대한 이해도를 높이기 위한 전초작업 정도로 생각하면 편리할 것 같다.

먼저 GUI로는 tkinter를 이용할 것이고 타이머를 제작해보면 어느정도 tkinter에 대해 전반적으로 알 수 있다고 생각한다.

tkinter를 배우는 순서는 gird 등등 배치를 배우고 나면 버튼 매칭하기 버튼에 트리거 되는 함수 짜기
마지막으로 window.after 이런 식으로 생각하면 편할 것이다.
~~~python
from tkinter import *
from tkinter import messagebox
import math
import itertools
# ---------------------------- CONSTANTS ------------------------------- #
PINK = "#e2979c"
RED = "#e7305b"
GREEN = "#9bdeac"
YELLOW = "#f7f5dd"
FONT_NAME = "Courier"
WORK_MIN = 25
SHORT_BREAK_MIN = 5
LONG_BREAK_MIN = 20
reps = 0
timer = None
pause = False
toggle_pause_unpause = itertools.cycle(["pause","unpause"])
toggle_start_reset = itertools.cycle(["Reset","Start"])
# ---------------------------- TIMER RESET ------------------------------- # 
def reset_timer():
    window.after_cancel(timer)
    logo.config(text='Timer',fg=GREEN)
    canvas.itemconfig(timer_text,text="00:00")
    check.config(text='')
    global  reps
    reps = 0


# ---------------------------- TIMER MECHANISM ------------------------------- # 
def start_timer():
    global reps
    reps += 1
    work_sec = WORK_MIN* 60
    short_break_sec = SHORT_BREAK_MIN *60
    long_break_sec = LONG_BREAK_MIN* 60
    if reps ==8:
        count_down(long_break_sec)#long
        logo.config(text='Finish',fg=RED)
        ask = messagebox.askquestion(title="Alarm", message="Finish")
        reset_timer()
    elif reps % 2 ==0:
        count_down(short_break_sec)#short_break_sec
        logo.config(text='Break',fg=PINK)
        on_pause_unpause_button_clicked()
        ask = messagebox.askquestion(title="Alarm", message="Take a break")
        if ask == 'yes':
            on_pause_unpause_button_clicked()

    elif reps ==1:
        count_down(work_sec)#work_sec
        logo.config(text='Work',fg=GREEN)
    else:
        count_down(work_sec)#work_sec
        logo.config(text='Work',fg=GREEN)
        on_pause_unpause_button_clicked()
        ask = messagebox.askquestion(title="Alarm", message="Go back to work")
        if ask == 'yes':
            on_pause_unpause_button_clicked()


def on_pause_unpause_button_clicked():
    action = next(toggle_pause_unpause)
    global pause
    if action == 'pause':
        pause = True
    elif action == 'unpause':
        pause = False

def toggle():
    start_reset_now = next(toggle_start_reset)
    start_button.config(text=start_reset_now)
    if start_reset_now == 'Reset':
        start_timer()
    elif start_reset_now == 'Start':
        reset_timer()


# ---------------------------- COUNTDOWN MECHANISM ------------------------------- # 
def count_down(count):
    global reps
    count_min = math.floor(count / 60)
    count_sec = count % 60
    if count_sec < 10:
        count_sec = '0'+str(count_sec)
    if count_sec ==0:
        count_sec ='00'

    canvas.itemconfig(timer_text,text = f"{count_min}:{count_sec}")
    if count >0:
        global timer
        if pause == False:
            timer = window.after(1000,count_down,count - 1)
        elif pause == True:
            timer = window.after(0,count_down,count)
    else:
        start_timer()
        check_time = math.floor((reps-1) / 2)
        check.config(text = '✓'*check_time)



# ---------------------------- UI SETUP ------------------------------- #


window = Tk()
window.title('Pomodoro')
window.wm_attributes("-topmost", 1)
window.config(padx=10,pady=10,bg=YELLOW)

#canvas
canvas = Canvas(width=100,height=112,bg=YELLOW, highlightthickness=0)
tomato_img = PhotoImage(file='tomato.png')
canvas.create_image(50,56,image=tomato_img)
timer_text = canvas.create_text(50,70,text='00:00',fill='white',font=(FONT_NAME,22,'bold'))
canvas.grid(column=1,row=1)



#label
logo = Label(text='Timer',fg=GREEN,bg=YELLOW,font=(FONT_NAME,27,'bold'))
logo.grid(column=1,row=0)

check = Label(bg=YELLOW,fg=GREEN,font=(FONT_NAME,11))
check.grid(column=1,row=3)
#button
start_button = Button(text="Start",command=toggle,highlightthickness=0)
start_button.grid(column=0,row=2)


pause_button = Button(text='pause',command=on_pause_unpause_button_clicked,highlightthickness=0)
pause_button.grid(column=2,row=2)



window.mainloop()
~~~
거두절미하고 [포모도르-타이머](https://replit.com/@Yusuengjae/pomodoro#main.py)를 클릭하면 쉽게 어떤 식으로 작동하는지 테스트 해볼 수 있다.

각각의 타이머가 시간이 다 끝나면 메시지 박스가 나오는 식이고 크기를 작게하고 바탕화면 항상 최상위에 위치하도록 하여서 시간확인이 용이하게 만들었다.
스타트 버튼을 누르면 리셋버튼으로 바뀌어서 혹시라도 있을 다중시작이 안되게 하였고 리셋 버튼을 누르면 리셋 후 다시 스타트버튼으로 바뀌게 되어있다.

그렇다면 이것을 매일 실행할때 어떻게 하면 바탕화면에서 exe파일로 바로 시작할 수 있을까?
정답은 pyinstaller다 


cmd창이나 terminal에서
~~~cmd
pyinstaller -w -F --icon=icon.ico -n pomodoro_timer.exe main.py
~~~
-w 윈도우콘솔 표시안함 (gui이기 때문에 굳이 필요없어서 표시안함)

-F exe파일 한개만 생성

-icon 아이콘

-n 파일 이름 난 pomodoro_timer로 했음

마지막에 main.py는 exe화할 파이썬 파일이다.

이렇게 하면 직접 exe파일로 바탕화면에서 킬 수 있다. 이미지도 pyinstaller안에 넣어주면 다른 사람들도 실행이 가능하다.

