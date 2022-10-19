---
layout: post
title: 알고리즘 유형 분석 - 자료구조
subtitle: 배열, 벡터, 연결리스트
categories: coding-test
description: algorithm Big-O notation for codingtest
tags: python coding-test
---

## Big O notation
빅오 표기법은 알고리즘의 효율성을 표기해주는 표기법이다.
빅오 표기법은 보통 알고리즘의 시간 복잡도와 공간 복잡도를 나타내는데 주로 사용 된다.
(시간 복잡도는 알고리즘의 시간 효율성을 의미하고, 공간 복잡도는 알고리즘의 공간(메모리) 효율성을 의미한다.)

![big-o-image](https://user-images.githubusercontent.com/22067624/195034704-22e7c1d5-1efc-48c7-b8bd-53ebefb6da19.png)




## 배열
배열을 선언하면 배열이 할당하는 메모리가 부여되고
~~~python
arr = [1,202,,123,31,1,45]
arr[2] = 5
~~~
arr 주소값 + 2 x 4byte(보통의경우) = 메모리 주소값
O(1) 임의 접근(random access) 탐색속도가 빠르다

~~~python
arr = [1,202,,123,31,1,45]
arr.insert(0,4)
~~~
O(len(arr)) 으로 연산복잡도가 증가한다 이유는 최악의 경우 첫번째 자리에 삽입의 경우 n번 뒤로 미루는 과정이 존재하기 때문이다

~~~python
arr = [1,202,,123,31,1,45]
del arr[0]
~~~
마찬가지로 O(len(arr)) 으로 연산복잡도가 증가한다 이유는 삽입과 마찬가지다

따라서 배열에서는 특정탐색의 경우에는 빠르게 진행되지만 
삽입과 삭제는 연산복잡도가 어느정도 있다는 것을 알 수 있다.

| 요약 | Big-O | 
| :------ |:--- | 
| 삽입, 삭제 | O(N) |
| 탐색 | O(1) | 
| 특징 | C++에서는 사이즈 변경불가 | 
      


## 벡터
~~~python
v=[]
v.append((123,456))
v.append((789,987))
print('size: ',len(v))
for p in v:
    print(p)
~~~

| 요약 | Big-O | 
| :------ |:--- | 
| 삽입, 삭제 | O(N) |
| 탐색 | O(1) | 
| 특징 | 동적배열(사이즈변경가능) |

## 연결 리스트
~~~C++
list<int> l;
l.emplace_back(0);
l.emplace_back(1);
l.emplace_back(2);
l.emplace_back(3);
printf("size: %d\n",l.size());
for (auto i : l)
  printf("%d\n",i);
~~~

| 요약 | Big-O | 
| :------ |:--- | 
| 삽입, 삭제 | O(1) |
| 탐색 | O(N) | 
| 특징 | 다른 자료구조들을 구현할때 쓰인다.<br>실제 문제에서는 잘 안 쓰임 |





