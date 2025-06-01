---
title: 💡 LeetCode 67 - Add Binary
date: 2025-03-01 22:50:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
>Given two binary strings a and b, return their sum as a binary string.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: a = "11", b = "1"
Output: "100" 
```

#### ✅ 예제 2
```bash
Input: a = "1010", b = "1011"
Output: "10101"
```


### 제약조건
- `1 <= a.length, b.length <= 104`
- a and b consist only of '0' or '1' characters.   
- Each string does not contain leading zeros except for the zero itself.


### 작성 코드
```java
import java.math.BigInteger;

class Solution {
	public String addBinary(String a, String b) {
		// 1. 변수 선언 및 초기화
		BigInteger numA = new BigInteger(a, 2);
		BigInteger numB = new BigInteger(b, 2);
		BigInteger result = numA.add(numB);
		
		// 2. 반환
		return result.toString(2);
	}
}
```
![](/assets/image/Pasted%20image%2020250528015222.png)
- `BigInteger`의 메서드들이 내부적으로 어떻게 동작하는지는 모르겠지만 굉장히 느리다.
- 가독성은 좋지만 주어지는 두 문자열이 항상 길이가 긴 경우가 아니라면 비효율적인 코드라고 생각한다.


### 개선 코드
```java
class Solution {
	public String addBinary(String a, String b) {
		// 1. 변수 선언 및 초기화
		int lengthA = a.length(); 
		int indexA = lengthA - 1; 
		int lengthB = b.length(); 
		int indexB = lengthB - 1; 
		boolean carryFlag = false;
		StringBuilder sb = new StringBuilder();
		
		// 2. 순회하며 덧셈 결과 문자열 생성
		while (indexA >= 0 || indexB >= 0) {
			int intA = indexA >= 0 ? a.charAt(indexA--) - '0' : 0;
			int intB = indexB >= 0 ? b.charAt(indexB--) - '0' : 0;
			int valueSum = intA + intB + (carryFlag ? 1 : 0);
			
			carryFlag = valueSum >= 2;
			
			sb.append(valueSum % 2);
		}
		
		// 3. 마지막 올림수 있을 경우 추가
		if (carryFlag) sb.append(1);
		
		// 4. 거꾸로 반환
		return sb.reverse().toString();
	}
}
```
![](/assets/image/Pasted%20image%2020250528015337.png)


### 회고
- 삼항 연산자를 피연산자에 사용할 수도 있다는 걸 알게 되었다.