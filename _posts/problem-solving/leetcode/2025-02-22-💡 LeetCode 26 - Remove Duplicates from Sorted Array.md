---
title: 💡 LeetCode 26 - Remove Duplicates from Sorted Array
date: 2025-02-22 23:44:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
> Given an integer array nums sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The relative order of the elements should be kept the same. Then return the number of unique elements in nums.
> 
> Consider the number of unique elements of nums to be k, to get accepted, you need to do the following things:
> 
> Change the array nums such that the first k elements of nums contain the unique elements in the order they were present in nums initially. 
> The remaining elements of nums are not important as well as the size of nums.
> Return k.
> Custom Judge:
> 
> The judge will test your solution with the following code:
```java
int[] nums = [...]; // Input array
int[] expectedNums = [...]; // The expected answer with correct length

int k = removeDuplicates(nums); // Calls your implementation

assert k == expectedNums.length;
for (int i = 0; i < k; i++) {
    assert nums[i] == expectedNums[i];
}
```
> If all assertions pass, then your solution will be accepted.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: nums = [1,1,2]
Output: 2, nums = [1,2,_]
Explanation: Your function should return k = 2, with the first two elements of nums being 1 and 2 respectively. It does not matter what you leave beyond the returned k (hence they are underscores).
```

#### ✅ 예제 2
```bash
Input: nums = [0,0,1,1,1,2,2,3,3,4]
Output: 5, nums = [0,1,2,3,4,_,_,_,_,_]
Explanation: Your function should return k = 5, with the first five elements of nums being 0, 1, 2, 3, and 4 respectively. It does not matter what you leave beyond the returned k (hence they are underscores).
```


### 제약조건
- `1 <= nums.length <= 3 * 104`
- `-100 <= nums[i] <= 100` 
- nums is sorted in non-decreasing order.


### 작성 코드
```java
class Solution {
	public int removeDuplicates(int[] nums) {
		// 1. 변수 선언 및 초기화
		Map<Integer, Integer> numMap = new HashMap<>();
		int idx = 0;
		
		// 2. 배열 순회하며 중복 아닌 값으로 재할당
		for (int num : nums) {
			if (!numMap.containsKey(num)) {
				numMap.put(num, 1);
				nums[idx++] = num;
			}
		}
		
		// 3. 반환
		return idx;
	}
}
```
![](/assets/image/Pasted%20image%2020250528003929.png)
- `HashMap`을 불필요하게 사용한 느낌이 들어서 코드를 개선 해보려고 한다.

### 개선 코드
```java
class Solution {
	public int removeDuplicates(int[] nums) {
		// 1. 변수 선언 및 초기화
		int idx = 0;
		int beforeNum = 999;
		
		// 2. 이전 값과 현재 값이 다를 경우 배열 갱신
		for (int num : nums) {
			if (beforeNum != num) nums[idx++] = num;
			beforeNum = num;
		}
		
		// 3. 반환
		return idx;
	}
}
```
![](/assets/image/Pasted%20image%2020250528004028.png)


### 회고
- `HashMap`을 사용하면 대부분의 문제가 해결되다 보니 무분별하게 사용을 하게 되는 것 같다.   
- 그런데 `HashMap`을 사용하면 값을 넣고 조회하는 부분에서 불필요한 시간 소요가 발생하다 보니 꼭 필요한 경우에만 사용할 수 있도록 해야겠다.   
- 값을 조회하는 부분에서 시간 소요가 발생하는 이유는 `HashCode` 연산이 발생하기 때문이다.