---
title: 🧩 Baekjoon 28702 - FizzBuzz
date: 2025-08-11 16:51:43 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - Baekjoon
  - B1
---

### 문제
> FizzBuzz 문제는 i = 1, 2, ... 에 대해 다음 규칙에 따라 문자열을 한 줄에 하나씩 출력하는 문제입니다.

-  i가 3의 배수이면서 5의 배수이면 “`FizzBuzz`”를 출력합니다.
-  i가 3의 배수이지만 5의 배수가 아니면 “`Fizz`”를 출력합니다.
-  i가 3의 배수가 아니지만 5의 배수이면 “`Buzz`”를 출력합니다.
-  i가 3의 배수도 아니고 5의 배수도 아닌 경우 i를 그대로 출력합니다.

> FizzBuzz 문제에서 연속으로 출력된 세 개의 문자열이 주어집니다. 
> 이때, 이 세 문자열 다음에 올 문자열은 무엇일까요?


### 입력
> FizzBuzz 문제에서 연속으로 출력된 세 개의 문자열이 한 줄에 하나씩 주어집니다. 
> 각 문자열의 길이는 8 이하입니다. 
> 입력이 항상 FizzBuzz 문제에서 연속으로 출력된 세 개의 문자열에 대응됨이 보장됩니다.


### 출력
> 연속으로 출력된 세 개의 문자열 다음에 올 문자열을 출력하세요. 
> 여러 문자열이 올 수 있는 경우, 아무거나 하나 출력하세요.


### 예제
#### ✅ 입력 1

```bash
Fizz
Buzz
11
```

#### ✅ 출력 1

```bash
Fizz
```

#### ✅ 입력 2

```bash
980803
980804
FizzBuzz
```

#### ✅ 출력 2

```bash
980806
```


### 작성 코드
```java
import java.io.BufferedReader;  
import java.io.IOException;  
import java.io.InputStreamReader;  
  
public class Main {  
	public static void main(String[] args) throws IOException {  
		// 1. 변수 선언 및 초기화  
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));  
		int result = -1;  
		
		// 2. 정수형일 경우 결과 도출  
		for (int i = 0; i < 3; i++) {  
			String input = br.readLine();  
			if (input.matches("-?\\d+")) {  
				result = Integer.parseInt(input) + 3 - i;  
				break;  
			}  
		}  
		
		// 3. 조건에 따라 출력  
		if (result % 3 == 0 && result % 5 == 0) {  
			System.out.println("FizzBuzz");  
		} else if (result % 3 == 0) {  
			System.out.println("Fizz");  
		} else if (result % 5 == 0) {  
			System.out.println("Buzz");  
		} else {  
			System.out.println(result);  
		}  
	}  
}
```
- 정규식을 사용해 정수형일 경우, 해당 시점의 인덱스와 값을 통해 결과를 도출하였다.