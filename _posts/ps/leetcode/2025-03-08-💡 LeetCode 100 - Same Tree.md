---
title: 💡 LeetCode 100 - Same Tree
date: 2025-03-08 23:38:00 +0900
categories:
  - PS
tags:
  - PS
  - LeetCode
  - EASY
---

### 문제
>Given the roots of two binary trees p and q, write a function to check if they are the same or not.
>Two binary trees are considered the same if they are structurally identical, and the nodes have the same value.


### 입출력 예제
#### ✅ 예제 1
![](/assets/image/Pasted%20image%2020250528022552.png)
```bash
Input: p = [1,2,3], q = [1,2,3]
Output: true
```

#### ✅ 예제 2
```bash
Input: p = [1,2], q = [1,null,2]
Output: false
```

#### ✅ 예제 3
```bash
Input: p = [1,2,1], q = [1,1,2]
Output: false
```


### 제약조건
- The number of nodes in both trees is in the range [0, 100].   
- `-10^4 <= Node.val <= 10^4`


### 작성 코드
```java
import java.util.Stack;

class Solution {
	public boolean isSameTree(TreeNode p, TreeNode q) {
		// 1. 변수 선언 및 초기화
		Stack<TreeNode> stackP = new Stack<>();
		Stack<TreeNode> stackQ = new Stack<>();
		
		// 2. 중위 순회
		while ((p != null || !stackP.isEmpty()) && (q != null || !stackQ.isEmpty())) {
			// 왼쪽 자식 노드로 이동
			while (p != null && q != null) {
				stackP.push(p);
				stackQ.push(q);
				
				p = p.left;
				q = q.left;
			}
			
			// 유효성 체크
			if (p == null ^ q == null) return false;
			
			// 부모 노드로 이동
			p = stackP.pop();
			q = stackQ.pop();
			
			// 유효성 체크
			if (p.val != q.val) return false;
			
			// 오른쪽 자식 노드로 이동
			p = p.right;
			q = q.right;
		}
		
		// 3. 반환
		return p == null && q == null;
	}
}
```
![](/assets/image/Pasted%20image%2020250528022819.png)


### 회고
- `XOR` 연산을 코드에 처음 써봤다.

```java
if ((p != null && q != null) && (p == null || q == null)) return false;
```
- 그동안 조건문을 작성할 때 `AND` 아니면 `OR`만 고려했는데, 앞으로는 `XOR`도 적용할 수 있는지 고려해야겠다.