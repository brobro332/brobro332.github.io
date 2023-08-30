---
title: "[Programmers] LV.2 멀리 뛰기"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.2]
published: true
toc: true
toc_sticky: true
---

#### ✅ 멀리 뛰기
```
효진이는 멀리 뛰기를 연습하고 있습니다. 
효진이는 한번에 1칸, 또는 2칸을 뛸 수 있습니다. 
칸이 총 4개 있을 때, 효진이는

(1칸, 1칸, 1칸, 1칸)
(1칸, 2칸, 1칸)
(1칸, 1칸, 2칸)
(2칸, 1칸, 1칸)
(2칸, 2칸)

의 5가지 방법으로 맨 끝 칸에 도달할 수 있습니다. 
멀리뛰기에 사용될 칸의 수 n이 주어질 때, 효진이가 끝에 도달하는 방법이 몇 가지인지 알아내, 
여기에 1234567를 나눈 나머지를 리턴하는 함수, solution을 완성하세요. 
예를 들어 4가 입력된다면, 5를 return하면 됩니다.
```


##### 1. 제한사항

```
- n은 1 이상, 2000 이하인 정수입니다.
```

##### 2. 구현 코드
```python
def solution(n):
    
    if n == 1:
        return 1
    
    result = [0] * (n + 1)
    
    result[1] = 1
    result[2] = 2
    
    for i in range(3, n + 1):
        result[i] = result[i - 2] + result[i - 1]
    
    return result[n] % 1234567
```

##### 3. 후기
앞으로는 점화식과 DP도 고려해야겠다고 느끼게 된 문제였다.