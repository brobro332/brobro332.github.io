---
title: 💡 LeetCode 66 - Plus One
date: 2025-02-26 22:19:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
>You are given a large integer represented as an integer array digits, where each digits[i] is the ith digit of the integer. 
>The digits are ordered from most significant to least significant in left-to-right order.
>The large integer does not contain any leading 0's.
>
>Increment the large integer by one and return the resulting array of digits.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: digits = [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123. Incrementing by one gives 123 + 1 = 124. Thus, the result should be [1,2,4].
```

#### ✅ 예제 2
```bash
Input: digits = [4,3,2,1]
Output: [4,3,2,2]
Explanation: The array represents the integer 4321. Incrementing by one gives 4321 + 1 = 4322. Thus, the result should be [4,3,2,2].
```

#### ✅ 예제 3
```bash
Input: digits = [9]
Output: [1,0]
Explanation: The array represents the integer 9. Incrementing by one gives 9 + 1 = 10. Thus, the result should be [1,0].
```


### 제약조건
- `1 <= digits.length <= 100`
- `0 <= digits[i] <= 9`
- digits does not contain any leading 0's.


### 작성 코드
```java
class Solution {
	public int[] plusOne(int[] digits) {
		// 1. 변수 선언 및 초기화
		int num = 0;
		int idx = 0;
		int[] result = new int[length];
		
		// 2. 파라미터 배열 int형 변수로 전환
		for (int digit : digits) num = num * 10 + digit;
		num += 1;
		
		// 3. 각 자릿수 구해서 배열에 적재
		int length = (int) Math.log10(num) + 1;
		while (num > 0) {
			result[idx++] = num / (int) Math.pow(10, --length);
			num %= Math.pow(10, length);
		}
		
		// 4. 반환
		return result;
	}
}
```
![](/assets/image/Pasted%20image%2020250528014434.png)
- 수학을 이용해서 풀어보고 싶었는데, 위의 반례가 있었다.
- `9_876_543_210`은 `int`형의 범위를 벗어나서 `Overflow`가 발생한다.
- `long`으로 형 변환해서 풀 수도 있겠지만, 다른 방식으로 접근했다.


### 개선 코드
```java
class Solution {
	public int[] plusOne(int[] digits) {
		// 1. 변수 선언 및 초기화
		int length = digits.length;
		
		// 2. 배열 거꾸로 순회하며 올림 발생하는 지 확인 및 처리
		for (int i=length - 1; i>=0; i--) {
			if (digits[i] + 1 >= 10) {
				// 1을 더했을 때 10 이상이라면 0 대입
				digits[i] = 0;
				if (i == 0) {
					// 최고 자릿수라면 배열 크기 늘리고 1 대입
					digits = Arrays.copyOf(digits, length + 1);
					digits[0] += 1;
					break;
				}
			} else {
				// 1을 더했을 때 10 미만이라면 1 덧셈
				digits[i] += 1;
				break;
			}
		}
		
		// 3. 반환
		return digits;
	}
}
```
![](/assets/image/Pasted%20image%2020250528014607.png)
- 배열의 사이즈를 늘려가며 해결하였다.