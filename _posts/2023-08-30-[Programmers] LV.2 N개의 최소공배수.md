---
title: "[Programmers] LV.2 N개의 최소공배수"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.2]
published: true
toc: true
toc_sticky: true
---

#### ✅ N개의 최소공배수
```
두 수의 최소공배수(Least Common Multiple)란 입력된 두 수의 배수 중 공통이 되는 가장 작은 숫자를 의미합니다. 
예를 들어 2와 7의 최소공배수는 14가 됩니다. 
정의를 확장해서, n개의 수의 최소공배수는 n 개의 수들의 배수 중 공통이 되는 가장 작은 숫자가 됩니다. 

n개의 숫자를 담은 배열 arr이 입력되었을 때 이 수들의 최소공배수를 반환하는 함수, solution을 완성해 주세요.
```


##### 1. 제한사항

```
- arr은 길이 1이상, 15이하인 배열입니다.
- arr의 원소는 100 이하인 자연수입니다.
```

##### 2. 구현 코드
```python
import math

def lcm(x, y):
    
    return (x * y) // math.gcd(x, y)

def solution(arr):
    
    while len(arr) >= 2:
        a = arr.pop()
        b = arr.pop()
        arr.append(lcm(a, b))
        
    return arr[0]
```

##### 3. 후기
두 값의 최소공배수를 구하는 공식과 N개의 최소공배수를 구하는 공식 정도를 알고 있으면 될 것 같다.  