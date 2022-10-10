---
layout: post
title: Python tictactoe 만들기
subtitle: 빠르게 만들기
categories: python
tags: python tictactoe miniproject
---

## 이번 mini_project는 간단한 tictactoe다

이전에 배운 git과 더불어 하나의 프로젝트 틱택토를 빠르게 완성해보자
~~~python
import random
number_list = [i for i in range(0,9)]
var_list = [' ' for _ in range(9) ]
game_is_on = True

def show_board():
  board= f"{var_list[0]} | {var_list[1]} | {var_list[2]}\n\
==========\n\
{var_list[3]} | {var_list[4]} | {var_list[5]}\n\
==========\n\
{var_list[6]} | {var_list[7]} | {var_list[8]}\n\n"
  print(board)
answer_list = [[0,1,2],[3,4,5],[6,7,8],[0,3,6],[1,4,7],[2,5,8],[0,4,8],[2,4,6]]

def end_game():
  global game_is_on
  if len(number_list)==0:
    print('Draw')
    game_is_on = False
  else:
    for answer in answer_list:
      amu_list =[]
      for i in answer:
        amu_list.append(var_list[i])
      if amu_list.count('O') == 3:
        print('player win')
        game_is_on = False
        return
      elif amu_list.count('X') == 3:
        print('Com win')
        game_is_on = False
        return

while game_is_on:
  user_input = input("your row and columns: ")
  row_num = int(user_input.split(' ')[0])
  col_num = int(user_input.split(' ')[1])
  order = (row_num-1)*3+col_num-1
  
  if order in number_list:
    number_list.remove(order)
    var_list[order] = 'O'
    order_com = random.choice(number_list)
    var_list[order_com] = 'X'
    number_list.remove(order_com)
    print(number_list)
    show_board()
    end_game()
  else: 
    print('error try again')
~~~
정말 간단한 버전으로 tictactoe를 빠르게 만들었다.

이 파일을 git add main.py 해서 git commit -m "초기버전" 또는 git commit -m "tictactoe-initial-commit" 등등 내가 원하는 대로 일단 commit 할 수 있다.

지금의 간단한 틱택토를 실제로 실행해보면 개선할 점이 많다는 것을 느낄 수 있다. 따라서 버전을 업그레이드 하면서 git에 차곡차곡 쌓아가보자

지금의 문제점은 다음과 같다
1. 플레이어 턴을 정할 수 없다.
2. 컴퓨터는 랜덤한 수를 둔다
3. print로 진행된다.
4. 유저의 입력이 불편하다

등등이 있다.

따라서 개선사항은
1. GUI로 만들기(3,4번 개선)
2. 로직개선(1,2번 개선)

의 순서로 버전을 업그레이드 하면서 git에 쌓아보자.

