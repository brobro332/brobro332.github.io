---
title: 💡 LeetCode 35 - Search Insert Position
date: 2025-02-24 18:32:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
> Given a sorted array of distinct integers and a target value, return the index if the target is found. 
> If not, return the index where it would be if it were inserted in order.  
> You must write an algorithm with O(log n) runtime complexity.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: nums = [1,3,5,6], target = 5
Output: 2
```

#### ✅ 예제 2
```bash
Input: nums = [1,3,5,6], target = 2
Output: 1
```

#### ✅ 예제 3
```bash
Input: nums = [1,3,5,6], target = 7
Output: 4
```


### 제약조건
- `1 <= nums.length <= 104`
- `-104 <= nums[i] <= 104`
- nums contains distinct values sorted in ascending order.
- `-104 <= target <= 104`


### 작성 코드
```java
class Solution {
	public int searchInsert(int[] nums, int target) {
		// 1. 변수 선언 및 초기화
		int left = 0;
		int right = nums.length - 1;
		
		// 2. 이진 탐색
		while (left <= right) {
			if (nums[left] == target) {
				// left 값과 동일하면 반환
				return left;
			} else if (nums[right] == target) {
				// right 값과 동일하면 반환
				return right;
			} else {
				// mid 값과 동일하면 반환
				int mid = (left + right) / 2; 
				if (nums[mid] == target) {
					return mid;
				} else if (nums[mid] > target) {
					right = mid - 1;
				} else {
					left = mid + 1;
				}
			}
		}
		
		// 3. 반환
		return left;
	}
}
```
![](/assets/image/Pasted%20image%2020250528012839.png)
- 문제에서 `O(log n)` 시간 복잡도를 허용한다고 하여 이진 탐색으로 코드를 작성했다.