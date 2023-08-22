---
title: "[Programmers] LV.1 숫자 짝꿍"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.1]
published: true
toc: true
toc_sticky: true
---

#### ✅ 숫자 짝꿍
```
두 정수 X, Y의 임의의 자리에서 공통으로 나타나는 정수 k(0 ≤ k ≤ 9)들을 이용하여 만들 수 있는 가장 큰 정수를 
두 수의 짝꿍이라 합니다(단, 공통으로 나타나는 정수 중 서로 짝지을 수 있는 숫자만 사용합니다). 
X, Y의 짝꿍이 존재하지 않으면, 짝꿍은 -1입니다. 
X, Y의 짝꿍이 0으로만 구성되어 있다면, 짝꿍은 0입니다.

예를 들어, X = 3403이고 Y = 13203이라면, 
X와 Y의 짝꿍은 X와 Y에서 공통으로 나타나는 3, 0, 3으로 만들 수 있는 가장 큰 정수인 330입니다. 
다른 예시로 X = 5525이고 Y = 1255이면 X와 Y의 짝꿍은 
X와 Y에서 공통으로 나타나는 2, 5, 5로 만들 수 있는 가장 큰 정수인 552입니다(X에는 5가 3개, Y에는 5가 2개 나타나므로 남는 5 한 개는 짝 지을 수 없습니다.)
두 정수 X, Y가 주어졌을 때, X, Y의 짝꿍을 return하는 solution 함수를 완성해주세요.
```


##### 1. 제한사항

- 3 ≤ X, Y의 길이(자릿수) ≤ 3,000,000입니다.
- X, Y는 0으로 시작하지 않습니다.
- X, Y의 짝꿍은 상당히 큰 정수일 수 있으므로, 문자열로 반환합니다.




##### 2. 구현 코드
```python
from collections import Counter

def solution(X, Y):
    
    x_cnt = Counter(X)
    y_cnt = Counter(Y)
    temp = [] 
    
    # X의 원소가 Y에 없을 경우 == Y의 원소가 X에 없을 경우
    cnt_sum = 0
    for i in X:
        cnt_sum += y_cnt[i]
    if cnt_sum == 0:
        return "-1"
    
    for i in range(len(X)):
        if X[i] in Y:
            temp.append(X[i])
    
    # 문자열 리스트화 및 정렬
    temp = sorted(list(set(temp)), reverse=True)
    
    print(temp)
     
    a = ''    
    for i in temp:
        if a == '' and i == '0':
            return "0"
        a += i * min(x_cnt[i], y_cnt[i])
        
    return str(a)
```

##### 3. 후기
첫 번째 시도로 시간 초과가 되었다. <br>
그 이유는 평소에도 자주 사용하는 int() 메소드 때문이다. <br>
3,000,000 길이의 문자열을 int로 형변환을 하게 되면 굉장한 시간이 소요된다. <br>
그래서 가능하면 긴 길이의 문자열을 형변환하는 일은 지양해야한다는 점을 알게 됐다.