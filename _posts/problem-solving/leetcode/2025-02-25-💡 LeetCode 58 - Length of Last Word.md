---
title: 💡 LeetCode 58 - Length of Last Word
date: 2025-02-25 22:43:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
>Given a string s consisting of words and spaces, return the length of the last word in the string.  
>A word is a maximal substring consisting of non-space characters only.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: s = "Hello World"
Output: 5
Explanation: The last word is "World" with length 5.
```

#### ✅ 예제 2
```bash
Input: s = "   fly me   to   the moon  "
Output: 4
Explanation: The last word is "moon" with length 4.
```

#### ✅ 예제 3
```bash
Input: s = "luffy is still joyboy"
Output: 6
Explanation: The last word is "joyboy" with length 6.
```


### 제약조건
- `1 <= s.length <= 104`
- s consists of only English letters and spaces ' '.   
- There will be at least one word in s.


### 작성 코드
```java
class Solution {
	public int lengthOfLastWord(String s) {
		// 1. 변수 선언 및 초기화
		String[] wordArr = s.split(" ");
		String lastWord = wordArr[wordArr.length - 1];
		
		// 2. 반환
		return lastWord.length();
	}
}
```
![](/assets/image/Pasted%20image%2020250528013414.png)