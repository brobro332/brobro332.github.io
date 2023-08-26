---
title: "[Programmers] LV.2 올바른 괄호"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.2]
published: true
toc: true
toc_sticky: true
---

#### ✅ 올바른 괄호
```
괄호가 바르게 짝지어졌다는 것은 '(' 문자로 열렸으면 반드시 짝지어서 ')' 문자로 닫혀야 한다는 뜻입니다. 
예를 들어 "()()" 또는 "(())()" 는 올바른 괄호입니다.
")()(" 또는 "(()(" 는 올바르지 않은 괄호입니다.

'(' 또는 ')' 로만 이루어진 문자열 s가 주어졌을 때, 
문자열 s가 올바른 괄호이면 true를 return 하고, 
올바르지 않은 괄호이면 false를 return 하는 solution 함수를 완성해 주세요.
```


##### 1. 제한사항

```
- 문자열 s의 길이 : 100,000 이하의 자연수
- 문자열 s는 '(' 또는 ')' 로만 이루어져 있습니다.
```

##### 2. 구현 코드
```python
from collections import deque

def solution(s):
    
    dq = deque(s)
    stack = []
    
    idx = -1
    for i in range(len(dq)):
        item = dq.popleft()
        stack.append(item)
        idx += 1
        if stack[idx - 1] =='(' and stack[idx] == ')':
            stack.pop()
            stack.pop()
            idx -= 2
            
    if stack:
        return False
    else:
        return True
```

##### 3. 후기
deque를 이용한 이유는 문자열 s에서 최하위 인덱스를 제거하는 로직에서 <br>
list.pop(0)을 이용하면 제거와 정렬의 로직이 있기 때문에 <br> 
문자열의 길이가 길어질 경우 시간 복잡도가 비효율적이기 때문이다. <br>
그래서 deque.popleft() 메소드를 이용하기 위해 deque를 사용했다.
