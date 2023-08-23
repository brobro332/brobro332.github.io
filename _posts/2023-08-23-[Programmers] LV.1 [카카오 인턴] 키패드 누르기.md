---
title: "[Programmers] LV.1 [카카오 인턴] 키패드 누르기"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.1]
published: true
toc: true
toc_sticky: true
---

#### ✅ 키패드 누르기
```
스마트폰 전화 키패드의 각 칸에 다음과 같이 숫자들이 적혀 있습니다.

이 전화 키패드에서 왼손과 오른손의 엄지손가락만을 이용해서 숫자만을 입력하려고 합니다.
맨 처음 왼손 엄지손가락은 * 키패드에 오른손 엄지손가락은 # 키패드 위치에서 시작하며, 
엄지손가락을 사용하는 규칙은 다음과 같습니다.

엄지손가락은 상하좌우 4가지 방향으로만 이동할 수 있으며 키패드 이동 한 칸은 거리로 1에 해당합니다.
왼쪽 열의 3개의 숫자 1, 4, 7을 입력할 때는 왼손 엄지손가락을 사용합니다.
오른쪽 열의 3개의 숫자 3, 6, 9를 입력할 때는 오른손 엄지손가락을 사용합니다.
가운데 열의 4개의 숫자 2, 5, 8, 0을 입력할 때는 두 엄지손가락의 현재 키패드의 위치에서 더 가까운 엄지손가락을 사용합니다.
4-1. 만약 두 엄지손가락의 거리가 같다면, 오른손잡이는 오른손 엄지손가락, 왼손잡이는 왼손 엄지손가락을 사용합니다.
순서대로 누를 번호가 담긴 배열 numbers, 왼손잡이인지 오른손잡이인 지를 나타내는 문자열 hand가 매개변수로 주어질 때, 
각 번호를 누른 엄지손가락이 왼손인 지 오른손인 지를 나타내는 연속된 문자열 형태로 return 하도록 solution 함수를 완성해주세요.
```


##### 1. 제한사항

- numbers 배열의 크기는 1 이상 1,000 이하입니다.
- numbers 배열 원소의 값은 0 이상 9 이하인 정수입니다.
- hand는 "left" 또는 "right" 입니다.
  - "left"는 왼손잡이, "right"는 오른손잡이를 의미합니다.
- 왼손 엄지손가락을 사용한 경우는 L, 오른손 엄지손가락을 사용한 경우는 R을 순서대로 이어붙여 문자열 형태로 return 해주세요.


##### 2. 구현 코드
```python
def solution(numbers, hand):
    
    result = ''
    
    location = {1:(0, 0), 2:(0, 1), 3:(0, 2), 4:(1, 0), 5:(1, 1), 6:(1, 2), 7:(2, 0), 8:(2, 1), 9:(2, 2), '*':(3, 0), 0:(3, 1), '#':(3, 2)}
    left = [1, 4, 7]
    right = [3, 6, 9]
    
    left_thumb = location['*']
    right_thumb = location['#']
    
    for i in range(len(numbers)):
        if numbers[i] in left:
            left_thumb = location[numbers[i]]
            result += "L"
        elif numbers[i] in right:
            right_thumb = location[numbers[i]]
            result += "R"
        else:
            if abs(left_thumb[0] - location[numbers[i]][0]) + abs(left_thumb[1] - location[numbers[i]][1]) == abs(right_thumb[0] - location[numbers[i]][0]) + abs(right_thumb[1] - location[numbers[i]][1]):
                if hand == "right":
                    right_thumb = location[numbers[i]]
                    result += "R"
                else:
                    left_thumb = location[numbers[i]]
                    result += "L"
            else:
                if abs(left_thumb[0] - location[numbers[i]][0]) + abs(left_thumb[1] - location[numbers[i]][1]) > abs(right_thumb[0] - location[numbers[i]][0]) + abs(right_thumb[1] - location[numbers[i]][1]):
                    right_thumb = location[numbers[i]]
                    result += "R"
                else:
                    left_thumb = location[numbers[i]]
                    result += "L"
        
    return result
```

##### 3. 후기
맨하탄 거리 공식과 유클리드 거리 공식을 알고 있었어야하며, <br>
딕셔너리 자료구조를 이용해서 시간 복잡도를 줄일 수 있었다. 