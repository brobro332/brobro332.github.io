---
title: 💡 LeetCode 9 - Palindrome Number
date: 2025-02-18 21:41:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
> Given an integer x, return true if x is a palindrome, and false otherwise.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: x = 121
Output: true
Explanation: 121 reads as 121 from left to right and from right to left.
```

#### ✅ 예제 2
```bash
Input: x = -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.
```

#### ✅ 예제 3
```bash
Input: x = 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.
```


### 제약조건
- `-231 <= x <= 231 - 1`


### 작성 코드
```java
	class Solution {
	public boolean isPalindrome(int x) {
		// 1. 변수 선언 및 초기화
		int remainder;
		int originX = x;
		int reversedX = 0;
		
		// 2. 팰린드롬 수 판별
		while (x > 0) {
			reversedX *= 10;
			remainder = x % 10;
			reversedX += remainder;
			x /= 10;
		}
		
		// 3. 반환
		return originX == reversedX ? true : false;
	}
}
```
![](/assets/image/Pasted%20image%2020250527235155.png)


### 회고
- 팰린드롬 수를 구하는 방법은 크게 문자열 연산과 숫자 연산 두 가지 방법이 있는데, 문자열 연산은 속도도 느리고, 메모리를 많이 잡아먹는다.
- 즉 문자열 연산이 아닌 숫자형 변수를 통해 나머지 연산을 하는 방법이 가장 효율적이다.