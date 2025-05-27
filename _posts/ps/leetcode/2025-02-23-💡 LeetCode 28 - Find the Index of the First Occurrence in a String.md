---
title: 💡 LeetCode 28 - Find the Index of the First Occurrence in a String
date: 2025-02-23 21:55:00 +0900
categories:
  - PS
tags:
  - PS
  - LeetCode
  - EASY
---

### 문제
> Given two strings needle and haystack, return the index of the first occurrence of needle in haystack, or -1 if needle is not part of haystack.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: haystack = "sadbutsad", needle = "sad"
Output: 0
Explanation: "sad" occurs at index 0 and 6. The first occurrence is at index 0, so we return 0.
```

#### ✅ 예제 2
```bash
Input: haystack = "leetcode", needle = "leeto"
Output: -1
Explanation: "leeto" did not occur in "leetcode", so we return -1.​
```


### 제약조건
- `1 <= haystack.length, needle.length <= 104`
- haystack and needle consist of only lowercase English characters.


### 작성 코드
```java
class Solution {
	public int strStr(String haystack, String needle) {
		// 반환
		return haystack.indexOf(needle);
	}
}
```
![](/assets/image/Pasted%20image%2020250528012258.png)


### 회고
- 필자는 `String.indexOf()` 메서드를 잘 안 쓰는데, 최근 `Tistory` 이웃 님의 포스팅에서 해당 메서드를 사용하여 문제 풀이한 코드를 보게 돼서 적용해보았다.
- 스스로 코드 짜는 것도 중요하지만 다른 분들이 어떤 아이디어를 갖고 코드를 작성하는 지도 살펴보는 것이 공부가 많이 되는 것 같다.  