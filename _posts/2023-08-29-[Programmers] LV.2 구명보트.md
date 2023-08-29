---
title: "[Programmers] LV.2 구명보트"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.2]
published: true
toc: true
toc_sticky: true
---

#### ✅ 구명보트
```
무인도에 갇힌 사람들을 구명보트를 이용하여 구출하려고 합니다. 
구명보트는 작아서 한 번에 최대 2명씩 밖에 탈 수 없고, 무게 제한도 있습니다.

예를 들어, 사람들의 몸무게가 [70kg, 50kg, 80kg, 50kg]이고 
구명보트의 무게 제한이 100kg이라면 2번째 사람과 4번째 사람은 같이 탈 수 있지만 
1번째 사람과 3번째 사람의 무게의 합은 150kg이므로 구명보트의 무게 제한을 초과하여 같이 탈 수 없습니다.

구명보트를 최대한 적게 사용하여 모든 사람을 구출하려고 합니다.

사람들의 몸무게를 담은 배열 people과 구명보트의 무게 제한 limit가 매개변수로 주어질 때, 
모든 사람을 구출하기 위해 필요한 구명보트 개수의 최솟값을 return 하도록 solution 함수를 작성해주세요.
```


##### 1. 제한사항

```
- 무인도에 갇힌 사람은 1명 이상 50,000명 이하입니다.
- 각 사람의 몸무게는 40kg 이상 240kg 이하입니다.
- 구명보트의 무게 제한은 40kg 이상 240kg 이하입니다.
- 구명보트의 무게 제한은 항상 사람들의 몸무게 중 최댓값보다 크게 주어지므로 사람들을 구출할 수 없는 경우는 없습니다.
```

##### 2. 구현 코드
```python
from collections import deque

def solution(people, limit):
    
    cnt = 0
    dq = deque(sorted(people))
    while len(dq) > 0:

        if len(dq) == 1:  
            dq.pop()
            cnt += 1
            break
        else:
            if dq[0] + dq[-1] > limit:
                dq.pop()
                cnt += 1
            else:
                dq.pop()
                dq.popleft()
                cnt += 1
    return cnt
```

##### 3. 후기
그리디 문제라서 마냥 쉬운 문제라고 생각했는데, <br>
최근 푼 문제 중 가장 어려웠다. <br>
투 포인터 방식의 접근 유형을 알게 되었다. 