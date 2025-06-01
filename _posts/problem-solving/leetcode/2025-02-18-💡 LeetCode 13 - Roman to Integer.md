---
title: 💡 LeetCode 13 - Roman to Integer
date: 2025-02-18 22:55:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
> Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.
```bash
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000
```
> For example, 2 is written as II in Roman numeral, just two ones added together. 
> 12 is written as XII, which is simply X + II. 
> The number 27 is written as XXVII, which is XX + V + II.
> 
> Roman numerals are usually written largest to smallest from left to right. 
> However, the numeral for four is not IIII. Instead, the number four is written as IV. 
> Because the one is before the five we subtract it making four. 
> The same principle applies to the number nine, which is written as IX. 
> There are six instances where subtraction is used:
- I can be placed before V (5) and X (10) to make 4 and 9. 
- X can be placed before L (50) and C (100) to make 40 and 90. 
- C can be placed before D (500) and M (1000) to make 400 and 900.
> Given a roman numeral, convert it to an integer.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: s = "III"
Output: 3
Explanation: III = 3.
```

#### ✅ 예제 2
```bash
Input: s = "LVIII"
Output: 58
Explanation: L = 50, V= 5, III = 3.
```

#### ✅ 예제 3
```bash
Input: s = "MCMXCIV"
Output: 1994
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.
```


### 제약조건
- `1 <= s.length <= 15`
- s contains only the characters ('I', 'V', 'X', 'L', 'C', 'D', 'M').
- It is guaranteed that s is a valid roman numeral in the range [1, 3999].


### 작성 코드
```java
class Solution {
	public int romanToInt(String s) {
		// 1. 변수 선언 및 초기화
		Map<String, Integer> romanNumMap = new HashMap<>();
		String[] romans = {"I", "V", "X", "L", "C", "D", "M"};
		int result = 0;
		int num = 1;
		int idx = 0;
		String beforeS = "";
		String nowS = "";
		
		// 2. 로마 숫자 해시맵 저장
		for (String roman : romans) {
			romanNumMap.put(roman, num);
			num *= (idx % 2 == 0) ? 5 : 2;
			idx++;
		}
		
		// 3. 순회하며 결과 저장
		for (int i=0; i<s.length(); i++) {
			nowS = String.valueOf(s.charAt(i));
			if (beforeS.equals("I") && (nowS.equals("V") || nowS.equals("X"))) result -= 2;
			if (beforeS.equals("X") && (nowS.equals("L") || nowS.equals("C"))) result -= 20;
			if (beforeS.equals("C") && (nowS.equals("D") || nowS.equals("M"))) result -= 200;
			result += romanNumMap.get(nowS);
			beforeS = nowS;
		}
		
		// 4. 반환
		return result;
	}
}
```
![](/assets/image/Pasted%20image%2020250528000323.png)
- 반복문에서 `if` 문을 세 번이나 거쳐간다는 점이 신경 쓰여서 최적화 해보았다.


### 개선 코드
```java
class Solution {
	public int romanToInt(String s) {
		// 1. 변수 선언 및 초기화
		Map<Character, Integer> romanNumMap = new HashMap<>();
		char[] cArr = s.toCharArray();
		int prevNum = 0;
		int currentNum = 0;
		int result = 0;
		
		// 2. 로마 숫자 해시맵 저장
		romanNumMap.put('I', 1);
		romanNumMap.put('V', 5);
		romanNumMap.put('X', 10);
		romanNumMap.put('L', 50);
		romanNumMap.put('C', 100);
		romanNumMap.put('D', 500);
		romanNumMap.put('M', 1000);
		
		// 3. 순회하며 결과 값 저장
		for (char c : cArr) {
			// 현재 값 추출
			currentNum = romanNumMap.get(c);
			
			// 문제 조건에 해당할 경우 prevNum * 2 만큼 차감
			if (prevNum < currentNum && currentNum <= prevNum * 10) result -= prevNum * 2;
			
			// 결과 값에 더하고 이전 값 갱신
			result += currentNum;
			prevNum = currentNum;
		}
		
		// 4. 반환
		return result;
	}
}
```
![](/assets/image/Pasted%20image%2020250528000433.png)
- `for-each`문을 쓰기 위해 문제에서 주어진 문자열은 `char[]`로 만들었다.
- 불필요한 연산과 함수 호출을 없애기 위해 로마 숫자 `HashMap` 키를 `char`형으로 초기화했다.
- 조건문의 규칙을 찾아 `if`문 한 개로 최적화 했다.


### 회고
- 코드를 먼저 작성하고 이후에 리팩토링 하는 습관을 고쳐야겠다고 생각했다.