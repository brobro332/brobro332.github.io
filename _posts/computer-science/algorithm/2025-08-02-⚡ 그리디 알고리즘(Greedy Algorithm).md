---
title: ⚡ 그리디 알고리즘(Greedy Algorithm)
date: 2025-08-02 13:02:00 +0900
categories:
  - Computer-Science
tags:
  - Computer-Science
  - algorithm
  - Greedy
---

### 🔹 그리디 알고리즘이란?

#### ▫️ 개념  
- 문제를 해결할 때 매 단계에서 가장 최선이라고 판단되는 선택을 하는 알고리즘  
- 전체 최적해를 보장하지 않을 수도 있지만, 문제에 따라 매우 효율적이고 간단하게 구현 가능

#### ▫️ 특징  
- 현재 상황에서 최선의 선택을 하므로 탐욕적(`Greedy`)이라고 불림  
- 해를 찾는 과정에서 이전 결정에 영향을 받지 않음
- 즉, 무작정 모든 경우를 탐색하지 않음
- 일부 문제에 한해 최적해 보장


### 🔹 그리디 알고리즘의 적용 조건
#### ▫️ 탐욕 선택 속성  
- 문제를 여러 단계로 나눴을 때 각 단계에서 최적 선택이 전체 문제의 최적 해에 기여해야 함

#### ▫️ 최적 부분 구조  
- 문제의 최적 해가 부분 문제의 최적 해로 구성되어야 함


### 🔹 대표 문제 및 예시
#### ▫️ 거스름돈 문제  
- 가장 큰 단위 화폐부터 가능한 만큼 거슬러주는 방식으로 최소 동전 수 계산  
- 단, 화폐 단위가 특정 조건을 만족해야 최적해 보장

```java
public class Change {
	public static int getMinCoins(int amount, int[] coins) {
		int count = 0;
		for (int coin : coins) {
			count += amount / coin;
			amount %= coin;
		}
		return count;
	}
	
	public static void main(String[] args) {
		int[] coins = {500, 100, 50, 10};
		int amount = 1260;
		int result = getMinCoins(amount, coins);
		System.out.println("최소 동전 개수: " + result);
	}
}
```

#### ▫️ 활동 선택 문제
- 시작 시간과 종료 시간이 주어진 여러 활동 중 겹치지 않는 최대 개수 선택
- 종료 시간이 빠른 활동부터 선택하는 것이 최적해

```java
import java.util.Arrays;
import java.util.Comparator;

public class ActivitySelection {
	static class Activity {
		int start, end;
		Activity(int start, int end) {
			this.start = start;
			this.end = end;
		}
	}
	
	public static int maxActivities(Activity[] activities) {
		Arrays.sort(activities, Comparator.comparingInt(a -> a.end));
		int count = 0;
		int lastEnd = 0;
		
		for (Activity act : activities) {
			if (act.start >= lastEnd) {
				count++;
				lastEnd = act.end;
			}
		}
		return count;
	}
	
	public static void main(String[] args) {
		Activity[] activities = {
			new Activity(1, 4),
			new Activity(3, 5),
			new Activity(0, 6),
			new Activity(5, 7),
			new Activity(8, 9),
			new Activity(5, 9)
		};
		int result = maxActivities(activities);
		System.out.println("최대 선택 가능한 활동 개수: " + result);
	}
}
```

### 🔹 그리디 알고리즘의 한계와 주의사항
- 모든 문제에 적용할 수 있는 것은 아님
- 탐욕 선택이 전체 최적 해로 이어지는지 문제의 특성을 반드시 검토해야 함
- 최적 해를 보장하지 못하는 경우 백트래킹이나 동적 계획법을 고려


### 🔹 마무리
- 그리디 알고리즘은 간단하고 빠른 해결책을 제공하지만, 문제에 맞게 적용하는 것이 중요함
- 대표 문제들을 연습하며 탐욕 선택 속성과 최적 부분 구조를 파악하는 것이 핵심이다.