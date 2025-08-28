---
title: 🧩 Baekjoon 1011 - Fly me to the Alpha Centauri
date: 2025-04-21 00:12:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - Baekjoon
  - G5
---

### 문제
> 우현이는 어린 시절, 지구 외의 다른 행성에서도 인류들이 살아갈 수 있는 미래가 오리라 믿었다. 
> 그리고 그가 지구라는 세상에 발을 내려 놓은 지 23년이 지난 지금, 세계 최연소 ASNA 우주 비행사가 되어 새로운 세계에 발을 내려 놓는 영광의 순간을 기다리고 있다. 
> 그가 탑승하게 될 우주선은 Alpha Centauri라는 새로운 인류의 보금자리를 개척하기 위한 대규모 생활 유지 시스템을 탑재하고 있기 때문에, 그 크기와 질량이 엄청난 이유로 최신기술력을 총 동원하여 개발한 공간이동 장치를 탑재하였다. 
> 하지만 이 공간이동 장치는 이동 거리를 급격하게 늘릴 경우 기계에 심각한 결함이 발생하는 단점이 있어서, 이전 작동시기에 k광년을 이동하였을 때는 k-1 , k 혹은 k+1 광년만을 다시 이동할 수 있다. 
> 예를 들어, 이 장치를 처음 작동시킬 경우 -1 , 0 , 1 광년을 이론상 이동할 수 있으나 사실상 음수 혹은 0 거리만큼의 이동은 의미가 없으므로 1 광년을 이동할 수 있으며, 그 다음에는 0 , 1 , 2 광년을 이동할 수 있는 것이다. 
> ( 여기서 다시 2광년을 이동한다면 다음 시기엔 1, 2, 3 광년을 이동할 수 있다. )
![](/assets/image/Pasted%20image%2020250528222117.png)
> 김우현은 공간이동 장치 작동시의 에너지 소모가 크다는 점을 잘 알고 있기 때문에 x지점에서 y지점을 향해 최소한의 작동 횟수로 이동하려 한다. 
> 하지만 y지점에 도착해서도 공간 이동장치의 안전성을 위하여 y지점에 도착하기 바로 직전의 이동거리는 반드시 1광년으로 하려 한다. 
> 김우현을 위해 x지점부터 정확히 y지점으로 이동하는데 필요한 공간 이동 장치 작동 횟수의 최솟값을 구하는 프로그램을 작성하라.


### 입력
> 입력의 첫 줄에는 테스트케이스의 개수 T가 주어진다. 각각의 테스트 케이스에 대해 현재 위치 x 와 목표 위치 y 가 정수로 주어지며, x는 항상 y보다 작은 값을 갖는다. (0 ≤ x < y < 2^31)


### 출력
> 각 테스트 케이스에 대해 x지점으로부터 y지점까지 정확히 도달하는데 필요한 최소한의 공간이동 장치 작동 횟수를 출력한다.


### 예제
#### ✅ 입력 1

```bash
3 
0 3 
1 5 
45 50
```

#### ✅ 출력 1

```bash
3 
3 
4
```


### 작성 코드

```java
public class Main {
	public static void main(String[] args) throws IOException {
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		int t = Integer.parseInt(br.readLine());
		
		for (int i = 0; i < t; i++) {
			StringTokenizer st = new StringTokenizer(br.readLine());
			int x = Integer.parseInt(st.nextToken());
			int y = Integer.parseInt(st.nextToken());
			int count = 0;
			
			if (x == y) {
				System.out.println(count);
				continue;
			}
			
			bfs(x, y);
		}
	}
	
	private static void bfs(int x, int y) {
		int distance = y - x;
		boolean[] visited = new boolean[distance + 1];
		Deque<int[]> queue = new ArrayDeque<>();
		queue.addLast(new int[]{x, 0, 0});
		visited[0] = true;
		
		while (!queue.isEmpty()) {
			int[] element = queue.pollFirst();
			int prev = element[0];
			int count = element[1];
			int move = element[2];
			
			if (prev == y - 1) {
				System.out.println(count + 1);
				return;
			}
			
			for (int d = move - 1; d <= move + 1; d++) {
				if (d <= 0) continue;
				
				int current = prev + d;
				
				if (current < y && !visited[current - x]) {
					queue.addLast(new int[]{current, count + 1, d});
					visited[current - x] = true;
				}
			}
		}
	}
}
```
- `BFS`로 문제를 풀었으나, 메모리 초과 문제가 발생했다.
- 풀고 나니 문제 자체가 메모리 측면에서 `BFS`에 적합하지 않다는 걸 알게 되었다.


### 개선 코드

```java
/*
    1
    11
    111
    121
    1211
    1121
    1221
    12211
    11221
    12221
    ...
*/

public class Main {
	public static void main(String[] args) throws IOException {
		// 1. 변수 선언 및 초기화
		BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
		int t = Integer.parseInt(br.readLine());
		
		// 2. 테스트 케이스 순회
		for (int i = 0; i < t; i++) {
			// 지역 변수 선언 및 초기화
			StringTokenizer st = new StringTokenizer(br.readLine());
			int x = Integer.parseInt(st.nextToken());
			int y = Integer.parseInt(st.nextToken());
			int distance = y - x;
			int move = 1, count = 0, current = 0;
			
			// 이동 처리
			while (current < distance) {
				if (distance - current <= move) {
					count++;
					break;
				} else if (distance - current < move * 2) {
					count += 2;
					break;
				} else {
					count += 2;
					current += move++ * 2;
				}
			}
			
			// 출력
			System.out.println(count);
		}
	}
}
```
- 규칙을 찾아서 `Brute-force` 방식으로 풀었다.


### 회고
- 시간 날 때 더 나은 방법이 있는지 생각해보아야겠다.