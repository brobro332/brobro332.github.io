---
title: 💡 LeetCode 20 - Valid Parentheses
date: 2025-02-21 00:14:00 +0900
categories:
  - PS
tags:
  - PS
  - LeetCode
  - EASY
---

### 문제
> Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.
> An input string is valid if:  
1. Open brackets must be closed by the same type of brackets.  
2. Open brackets must be closed in the correct order.   
3. Every close bracket has a corresponding open bracket of the same type.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: s = "()" 
Output: true
```

#### ✅ 예제 2
```bash
Input: s = "()[]{}"
Output: true
```

#### ✅ 예제 3
```bash
Input: s = "(]" 
Output: false
```

#### ✅ 예제 4
```bash
Input: s = "([])"
Output: true
```


### 제약조건
- `1 <= s.length <= 104`
- s consists of parentheses only '()[]{}'.


### 작성 코드
```java
class Solution {
	public boolean isValid(String s) {
		// 1. 변수 선언 및 초기화
		List<Character> cList = new ArrayList<>();
		Map<Character, Character> solutionMap = new HashMap();
		
		// 2. 조건 해시맵 저장
		solutionMap.put('(', ')');
		solutionMap.put('{', '}');
		solutionMap.put('[', ']');
		
		// 3. 문자열 순회하며 스택에 적재 및 제거 
		int length = 0;
		for (int i=0; i<s.length(); i++) {
			cList.add(s.charAt(i));
			length = cList.size();
			
			if (length > 1 && solutionMap.get(cList.get(length - 2)) == cList.get(length - 1)) {
				cList.remove(cList.size() - 1);
				cList.remove(cList.size() - 1);
			}
		}
		
		// 4. 반환
		return cList.size() == 0 ? true : false;
	}
}
```
![](/assets/image/Pasted%20image%2020250528002030.png)
- 코드가 지저분하고, `Runtime`을 줄이고자 코드를 개선 해보았다.


### 개선 코드
```java
class Solution {
	public boolean isValid(String s) {
		// 1. 변수 선언 및 초기화
		Deque<Character> cDeque = new ArrayDeque<>();
		Map<Character, Character> solutionMap = new HashMap();
		
		// 2. 조건 해시맵 저장
		solutionMap.put(')', '(');
		solutionMap.put('}', '{');
		solutionMap.put(']', '[');
		
		// 3. 문자열 순회하며 스택에 적재 및 제거 
		for (char c : s.toCharArray()) {
			if (!cDeque.isEmpty() && solutionMap.containsKey(c)) {
				if (solutionMap.get(c) == cDeque.peek()) {
					cDeque.pop();
				} else {
					// 문제 조건에 부합하지 않는다면 적재
					cDeque.push(c);
				}    
			} else {
				// 덱이 비었거나 닫는 괄호라면 적재
				cDeque.push(c);
			}
		}
		
		// 4. 반환
		return cDeque.isEmpty();
	}
}
```
![](/assets/image/Pasted%20image%2020250528002155.png)
- `List`에 데이터 추가 시 배열 복사가 일어날 수 있으므로 `Deque` 자료구조 사용
- `for-each`문 사용


### 회고
- `peekLast()`가 맨 뒤 요소 값을 추출한다고 하여 `top` 값을 가져오는 줄 알았는데,  알고 보니 `bottom` 값을 가져오는 메서드였다.  
- `Stack`은 선입후출이므로 입구 기준으로 `last`는 `bottom`이고, `first`는 `top`을 의미한다. 
- 즉 `Stack`의 `top` 요소 값을 제거하지 않고 추출하려면 `peek()` 메서드를 사용하면 된다. 