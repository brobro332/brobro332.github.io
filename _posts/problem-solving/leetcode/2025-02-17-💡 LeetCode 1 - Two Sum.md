---
title: 💡 LeetCode 1 - Two Sum
date: 2025-02-17 22:24:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
> Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.  
> 
> You may assume that each input would have exactly one solution, and you may not use the same element twice.  
> 
> You can return the answer in any order.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Explanation: Because nums[0] + nums[1] == 9, we return [0, 1].
```

#### ✅ 예제 2
```bash
Input: nums = [3,2,4], target = 6
Output: [1,2]
```

#### ✅ 예제 3
```bash
Input: nums = [3,3], target = 6
Output: [0,1]
```


### 제약조건
- `2 <= nums.length <= 104`
- `-109 <= nums[i] <= 109`
- `-109 <= target <= 109`
- Only one valid answer exists.


### 작성 코드
```java
class Solution {
	public int[] twoSum(int[] nums, int target) {
		int arrLength = nums.length;
		int[] result = new int[2];
		for (int i=0; i<arrLength; i++) {
			for (int j=i+1; j<arrLength; j++) {
				if (nums[i] + nums[j] == target) {
					result[0] = i;
					result[1] = j;
					break;
				}
			}
		}
		return result;
	}
}
```
![](/assets/image/Pasted%20image%2020250527234407.png)
- 다른 사람들보다 속도가 굉장히 낮아서 개선해보고자 한다.

### 개선 코드
```java
class Solution {
	public int[] twoSum(int[] nums, int target) {
		// 1. 변수 선언 및 초기화
		Map<Integer, Integer> idxMap = new HashMap<>();
		int[] result = new int[2];
		
		// 2. 순회하며 두 값의 합이 target인 경우 탐색
		int idx = 0;
		for (int num : nums) {
			// 해당 원소 키 값이 존재한다면 반환
			if (idxMap.containsKey(num)) result = new int[] { idxMap.get(num), idx };
			
			// target - nums[idx]와 idx의 키-값쌍 저장
			int theOtherNum = target - nums[idx];
			idxMap.put(theOtherNum, idx);
			
			// 인덱스 증가
			idx++;
		}
		
		// 3. 반환
		return result;
	}
}
```
![](/assets/image/Pasted%20image%2020250527234554.png)
- 이중 `for`문이 문제라고 생각해서 단일 `for`문과 함께 `HashMap`을 사용하였다.
- 배열을 순회하며 현재 값이 이전 값들의 `target - nums[idx]`에 해당하는지 확인한다.


### 회고
- 더 빠르게 하려면 어떻게 해야 할 지 궁금하다.