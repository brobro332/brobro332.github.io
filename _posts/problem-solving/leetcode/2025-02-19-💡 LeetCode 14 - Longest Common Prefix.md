---
title: 💡 LeetCode 14 - Longest Common Prefix
date: 2025-02-19 22:15:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
> Write a function to find the longest common prefix string amongst an array of strings.
> If there is no common prefix, return an empty string "".


### 입출력 예제
#### ✅ 예제 1
```bash
Input: strs = ["flower","flow","flight"]
Output: "fl"
```

#### ✅ 예제 2
```bash
Input: strs = ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.
```


### 제약조건
- `1 <= strs.length <= 200`
- `0 <= strs[i].length <= 200`
- strs[i] consists of only lowercase English letters if it is non-empty.


### 작성 코드
```java
class Solution {
	public String longestCommonPrefix(String[] strs) {
		// 1. 변수 선언 및 초기화
		String result = "";
		int shortestLength = 200;
		StringBuilder sb = new StringBuilder();
		
		// 2. 배열 요소 중 문자열 최소 길이 추출
		for (String str : strs) {
			int newStrLength = str.length();
			if (str.length() < shortestLength) shortestLength = newStrLength;
		}
		
		// 3. 가장 짧은 문자열 만큼 prefix 판별 
		for (int i=0; i<shortestLength; i++) {
			// 플래그 및 문자 초기화
			boolean prefixFlag = true;
			char c = strs[0].charAt(i);
			
			// 문자열 배열 순회하며 동일 여부 확인
			for (int j=0; j<strs.length; j++) {
				if (c != strs[j].charAt(i)) prefixFlag = false;
			}
			
			// 플래그가 거짓이라면 순회 탈출
			if (!prefixFlag) break;
			
			// 플래그가 참이라면 결과에 추가
			sb.append(c);
		}
		
		// 4. 반환
		result = sb.toString();
		return "".equals(result) ? "" : result;
	}
}
```
![](/assets/image/Pasted%20image%2020250528001208.png)
- 문자열 배열에서 각 문자열의 길이가 모두 다를 것이다.
- `NullPointerException`을 피하기 위해 문자열의 최소 길이 만큼만 순회했다.


### 개선 코드
```java
import java.util.Arrays;

public class LongestCommonPrefix {
	public static String longestCommonPrefix(String[] strs) {
		if (strs == null || strs.length == 0) {
			return "";
		}
		
		Arrays.sort(strs);
		
		String first = strs[0];
		String last = strs[strs.length - 1];
		
		int i = 0;
		
		while (i < first.length() && i < last.length() && first.charAt(i) == last.charAt(i)) {
			i++;
		}
		
		return first.substring(0, i);
	}
	
	public static void main(String[] args) {
		String[] input = {"flower", "flow", "flight"};
		System.out.println("공통 접두사: " + longestCommonPrefix(input));  // 출력: "fl"
	}
}
```
- 이중 `for`문을 없애고 싶어서 `ChatGPT`에 물어봤는데, 참신한 아이디어가 있었다. 
- 문자열 배열을 사전 순으로 정렬하고 첫 번째 문자열과 마지막 문자열만 확인하는 것이다.