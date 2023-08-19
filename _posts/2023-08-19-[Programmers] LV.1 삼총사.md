---
title: "[Programmers] LV.1 삼총사"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.1]
published: true
toc: true
toc_sticky: true
---

#### ✅ 삼총사
```
한국중학교에 다니는 학생들은 각자 정수 번호를 갖고 있습니다.
이 학교 학생 3명의 정수 번호를 더했을 때 0이 되면 3명의 학생은 삼총사라고 합니다. 
예를 들어, 5명의 학생이 있고, 각각의 정수 번호가 순서대로 -2, 3, 0, 2, -5일 때, 
첫 번째, 세 번째, 네 번째 학생의 정수 번호를 더하면 0이므로 세 학생은 삼총사입니다. 
또한, 두 번째, 네 번째, 다섯 번째 학생의 정수 번호를 더해도 0이므로 세 학생도 삼총사입니다. 
따라서 이 경우 한국중학교에서는 두 가지 방법으로 삼총사를 만들 수 있습니다.

한국중학교 학생들의 번호를 나타내는 정수 배열 number가 매개변수로 주어질 때, 
학생들 중 삼총사를 만들 수 있는 방법의 수를 return 하도록 solution 함수를 완성하세요.
```


##### 1. 제한사항

- 3 ≤ number의 길이 ≤ 13
- -1,000 ≤ number의 각 원소 ≤ 1,000
- 서로 다른 학생의 정수 번호가 같을 수 있습니다.



##### 2. 구현 코드
```python
def solution(number):

    sum = 0
    cnt = 0
    
    for i in range(len(number)):
        for j in range(i + 1, len(number)):
            for l in range(j + 1, len(number)):   
                sum = number[i] + number[j] + number[l]
                if sum == 0:
                    cnt += 1
                sum = 0
    
    return cnt
```

##### 3. 후기
어느덧 89개의 문제를 풀어간다. <br>
프로그래머스를 처음 방문했을 때보다 많이 나아졌지만 아직도 미숙하다고 느껴진다. <br>
이번 문제는 조합에 대한 문제이다. <br>
순서없는 중복 제거에 머리가 아파 인터넷과 질문하기 게시판을 참고했다. <br>
이번 문제를 풀면서 특히 코딩은 수학적인 사고가 중요하다는 것을 깨달았다.