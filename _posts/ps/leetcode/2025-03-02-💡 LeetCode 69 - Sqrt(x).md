---
title: 💡 LeetCode 69 - Sqrt(x)
date: 2025-03-02 09:52:00 +0900
categories:
  - PS
tags:
  - PS
  - LeetCode
  - EASY
---

### 문제
>Given a non-negative integer x, return the square root of x rounded down to the nearest integer. The returned integer should be non-negative as well.   
>
>You must not use any built-in exponent function or operator.   
>
>For example, do not use pow(x, 0.5) in c++ or x ** 0.5 in python.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: x = 4
Output: 2
Explanation: The square root of 4 is 2, so we return 2.
```

#### ✅ 예제 2
```bash
Input: x = 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since we round it down to the nearest integer, 2 is returned.
```


### 제약조건
- `0 <= x <= 2^31 - 1`


### 작성 코드
```java
class Solution {
	public int mySqrt(int x) {
		// 1. 변수 선언 및 초기화
		long i = 2;
		if (x < 2) return x;
		
		// 2. 순회
		while (i < x/2) {
			if (i * i >= x) break;
			i++;
		}
		
		// 3. 반환
		return i * i > x ? (int)i - 1 : (int)i;
	}
}
```
![](/assets/image/Pasted%20image%2020250528015753.png)
- `x`가 커질수록 굉장히 많은 순회를 돌게 된다.


### 개선 코드
```java
class Solution {
	public int mySqrt(int x) {
		// 1. 변수 선언 및 초기화
		int left = 0;
		int right = x;
		
		// 2. 이진탐색
		while (left <= right) {
			int mid = (left + right) / 2;
			long square = (long) mid * mid;
			
			if (square == x) return mid;
			if (square < x) {
				left = mid + 1;
			} else {
				right = mid - 1;
			}
		}
		
		// 3. 반환
		return left - 1;
	}
}
```
![](/assets/image/Pasted%20image%2020250528015834.png)
- 이진 탐색으로 시간 복잡도를 낮춰 보았다.


### 회고
```java
int mid = (left + right) / 2;
```
- 상기 코드는 `left`와 `right`가 둘 다 큰 값이면 `mid`를 구하는 과정에서 `Overflow`가 발생하기 쉽다.
- 반면 다음 코드는 중간 값을 구하는 논리에 벗어나지도 않고, `Overflow`를 방지할 수 있다.

```java
/* 
 * int d = (right - left) / 2;
 * int mid = left + d;
 */
int mid = left + (right - left) / 2;
```
- (`right - left) / 2`는 중간 값까지 이동 거리 `d`를 의미한다.
- 즉 `left`를 현재 좌표라고 하면 `left + d`가 중간 값이 된다.
- 상기 코드가 `Overflow`에서 안전한 이유는 나누기 연산을 덧셈 연산보다 먼저 하기 때문이다.
- 이 문제를 더 빠르게 풀려면 `Newton–Raphson` 알고리즘을 사용해야 한다고 한다.
- 제곱근 같이 미분 가능한 함수의 근을 빠르게 찾을 때 사용된다고 하는데, 근삿값을 구하는 공식이라서 부동소수점 오차가 있을 수 있고, 해가 여러 개 있을 경우 하나의 해만 찾을 수 있다는 한계가 있다고 한다.