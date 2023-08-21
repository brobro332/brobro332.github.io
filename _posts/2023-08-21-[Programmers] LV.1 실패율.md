---
title: "[Programmers] LV.1 실패율"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.1]
published: true
toc: true
toc_sticky: true
---

#### ✅ 실패율
```
슈퍼 게임 개발자 오렐리는 큰 고민에 빠졌다. 
그녀가 만든 프랜즈 오천성이 대성공을 거뒀지만, 요즘 신규 사용자의 수가 급감한 것이다. 
원인은 신규 사용자와 기존 사용자 사이에 스테이지 차이가 너무 큰 것이 문제였다.

이 문제를 어떻게 할까 고민 한 그녀는 동적으로 게임 시간을 늘려서 난이도를 조절하기로 했다. 
역시 슈퍼 개발자라 대부분의 로직은 쉽게 구현했지만, 실패율을 구하는 부분에서 위기에 빠지고 말았다. 
오렐리를 위해 실패율을 구하는 코드를 완성하라.

실패율은 다음과 같이 정의한다.
스테이지에 도달했으나 아직 클리어하지 못한 플레이어의 수 / 스테이지에 도달한 플레이어 수
전체 스테이지의 개수 N, 게임을 이용하는 사용자가 현재 멈춰있는 스테이지의 번호가 담긴 배열 stages가 매개변수로 주어질 때, 
실패율이 높은 스테이지부터 내림차순으로 스테이지의 번호가 담겨있는 배열을 return 하도록 solution 함수를 완성하라.
```


##### 1. 제한사항

- 스테이지의 개수 N은 1 이상 500 이하의 자연수이다.
- stages의 길이는 1 이상 200,000 이하이다.
- stages에는 1 이상 N + 1 이하의 자연수가 담겨있다.
  - 각 자연수는 사용자가 현재 도전 중인 스테이지의 번호를 나타낸다.
  - 단, N + 1 은 마지막 스테이지(N 번째 스테이지) 까지 클리어 한 사용자를 나타낸다.
- 만약 실패율이 같은 스테이지가 있다면 작은 번호의 스테이지가 먼저 오도록 하면 된다.
- 스테이지에 도달한 유저가 없는 경우 해당 스테이지의 실패율은 0 으로 정의한다.



##### 2. 구현 코드
```python
# 라이브러리 없이 주먹구구식 코드
def solution(N, stages):

    fail = []
    rank = []
    result = []

    for i in range(1, N + 1):
        a_cnt = 0
        n_cnt = 0
        for j in stages:
            # 스테이지에 도달한 플레이어 수 
            if i <= j:
                a_cnt += 1
                # 스테이지에 도달했으나 아직 클리어하지 못한 플레이어 수
                if i == j:
                    n_cnt += 1
        if a_cnt == 0:
            fail.append((i, 0))
        else:
            fail.append((i, n_cnt / a_cnt))

    rank = sorted(fail, key = lambda x:(-x[1], x[0]))

    for i in rank:
        result.append(i[0])

    return result
```

```python
# Counter 라이브러리를 사용한 코드
from collections import Counter

def solution(N, stages):
    
    temp = []
    result = []
    cnt = Counter(stages)
    arrive = len(stages) 
    
    for i in range(1, N + 1):
        if cnt[i] == 0:
            temp.append((i, 0))
            continue
        fail = cnt[i] / arrive
        temp.append((i, fail))
        arrive -= cnt[i]
    
    temp = sorted(temp, key = lambda x:(-x[1], x[0]))
    
    for i in temp:
        result.append(i[0])
        
    return result
```

##### 3. 후기
라이브러리의 중요성을 깨달았다. <br>
구현도 쉬워지고 시간 복잡도도 굉장히 쉽게 효과적으로 줄일 수 있다. <br>
라이브러리를 사용하지 않은 코드는 시간 복잡도가 커서 턱걸이로 통과된 느낌이 있었다. <br>
혹시나 시험 환경에서 제한될까봐 라이브러리를 지양했는데, <br>
라이브러리가 제한되지 않을 경우에는 라이브러리를 사용하지 않을 경우 <br>
얻게되는 패널티가 너무 커서 Counter, itertools의 사용법도 공부해야겠다고 느꼈다. 