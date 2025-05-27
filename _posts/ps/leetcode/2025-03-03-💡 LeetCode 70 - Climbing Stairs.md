---
title: 💡 LeetCode 70 - Climbing Stairs
date: 2025-03-03 07:30:00 +0900
categories:
  - PS
tags:
  - PS
  - LeetCode
  - EASY
---

### 문제
>You are climbing a staircase. It takes n steps to reach the top.   
>Each time you can either climb 1 or 2 steps. 
>In howmany distinct ways can you climb to the top?


### 입출력 예제
#### ✅ 예제 1
```bash
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

#### ✅ 예제 2
```bash
Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```


### 제약조건
- `1 <= n <= 45`


### 작성 코드
```java
class Solution {
	public int climbStairs(int n) {
		// 1. 유효성 체크 및 반환 
		if (n == 1) return 1;
		
		// 2. 배열 선언 및 초기화
		int[] dp = new int[n + 1];
		dp[1] = 1;
		dp[2] = 2;
		
		// 3. DP 처리
		for (int i=3; i<=n; i++) {
			dp[i] = dp[i - 2] + dp[i - 1];
		}
		
		// 4. 반환
		return dp[n];
	}
}
```
![](/assets/image/Pasted%20image%2020250528020809.png)
- 점화식은 다음과 같다.

```bash
f(n) = f(n - 1) + f(n - 2)
```


### 회고
- `DP`는 작은 문제들의 답을 조합해서 큰 문제를 해결하는 방식이다.
- 문제를 작은 문제로 나눌 때 중복 계산을 줄일 수 있도록 값을 재사용 해야 한다.