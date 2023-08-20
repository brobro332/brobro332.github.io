---
title: "[Programmers] LV.1 두 개 뽑아서 더하기"
excerpt: "Algorithm; Programmers;"
categories: 
- Algorithm
tags:
- [Algorithm, Programmers, LV.1]
published: true
toc: true
toc_sticky: true
---

#### ✅ 두 개 뽑아서 더하기
```
정수 배열 numbers가 주어집니다. 

numbers에서 서로 다른 인덱스에 있는 두 개의 수를 뽑아 더해서 만들 수 있는 모든 수를 배열에 오름차순으로 담아 return 하도록 solution 함수를 완성해주세요.
```


##### 1. 제한사항

- numbers의 길이는 2 이상 100 이하입니다.
  - numbers의 모든 수는 0 이상 100 이하입니다.



##### 2. 구현 코드
```python
def solution(numbers):
    
    result = []
    
    for i in range(len(numbers)):
        for j in range(i + 1, len(numbers)):
            result.append(numbers[i] + numbers[j])
            
    result_set = {*result}
    result = list(result_set)
    result.sort() 
    
    return result
```

##### 3. 후기
세트를 통해 중복성을 없애는 로직이 필요한 문제였다. <br>
문제는 정렬되어 있는 리스트더라도 세트로 변환하는 경우에는 순서가 존재하지 않는 세트의 특징으로 인해 <br>
다시 리스트로 변환할 경우 순서가 뒤죽박죽이 되는 것 같다. <br>
확신을 못하는 이유는 문제에서 주어지는 테스트 케이스는 정상 정렬이 되어있지만 <br>
답안을 제출할 경우 모든 테스트 케이스에서 통과하지 않기 때문이다. <br>
세트를 리스트로 변환하고서 정렬을 하니 정답 처리가 되었다.  