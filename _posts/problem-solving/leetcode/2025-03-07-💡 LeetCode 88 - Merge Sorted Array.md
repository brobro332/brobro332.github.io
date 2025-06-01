---
title: 💡 LeetCode 88 - Merge Sorted
date: 2025-03-07 22:04:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
>You are given two integer arrays nums1 and nums2, sorted in non-decreasing order, and two integers m and n, representing the number of elements in nums1 and nums2 respectively.
>
>Merge nums1 and nums2 into a single array sorted in non-decreasing order.
>
>The final sorted array should not be returned by the function, but instead be stored inside the array nums1. To accommodate this, nums1 has a length of m + n, where the first m elements denote the elements that should be merged, and the last n elements are set to 0 and should be ignored. nums2 has a length of n.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
Explanation: The arrays we are merging are [1,2,3] and [2,5,6]. The result of the merge is [1,2,2,3,5,6] with the underlined elements coming from nums1.
```

#### ✅ 예제 2
```bash
Input: nums1 = [1], m = 1, nums2 = [], n = 0
Output: [1]
Explanation: The arrays we are merging are [1] and []. The result of the merge is [1].
```

#### ✅ 예제 3
```bash
Input: nums1 = [0], m = 0, nums2 = [1], n = 1
Output: [1]
Explanation: The arrays we are merging are [] and [1]. The result of the merge is [1]. Note that because m = 0, there are no elements in nums1. The 0 is only there to ensure the merge result can fit in nums1.
```


### 제약조건
- `nums1.length == m + n`
- `nums2.length == n`
- `0 <= m, n <= 200`
- `1 <= m + n <= 200`
- `-109 <= nums1[i], nums2[j] <= 109`


### 작성 코드
```java
class Solution {
	public void merge(int[] nums1, int m, int[] nums2, int n) {
		// 1. 변수 선언 및 초기화
		int index1 = m - 1;
		int index2 = n - 1;
		int indexResult = m + n - 1;
		int value1 = 0; int value2 = 0;
		
		// 2. 순회하며 배열 역순으로 초기화
		while (indexResult >= 0) {
			if (index1 < 0) {
				nums1[indexResult--] = nums2[index2--];
			} else if (index2 < 0) {
				nums1[indexResult--] = nums1[index1--]; 
			} else {
				value1 = nums1[index1];
				value2 = nums2[index2];
				if (value1 >= value2) {
					nums1[indexResult--] = value1;
					index1--;
				} else {
					nums1[indexResult--] = value2;
					index2--;
				}
			}
		}
	}
}
```
![](/assets/image/Pasted%20image%2020250528021846.png)
- 역순으로 배열을 초기화하지 않는다면 배열 원소를 옮기는 과정도 추가된다.