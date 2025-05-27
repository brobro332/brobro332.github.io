---
title: 💡 LeetCode 101 - Symmetric Tree
date: 2025-03-11 20:44:00 +0900
categories:
  - PS
tags:
  - PS
  - LeetCode
  - EASY
---

### 문제
>Given the root of a binary tree, check whether it is a mirror of itself (i.e., symmetric around its center).


### 입출력 예제
#### ✅ 예제 1
![](/assets/image/Pasted%20image%2020250528023058.png)
```bash
Input: root = [1,2,2,3,4,4,3]
Output: true
```

#### ✅ 예제 2
![](/assets/image/Pasted%20image%2020250528023115.png)
```bash
Input: root = [1,2,2,null,3,null,3]
Output: false
```


### 제약조건
- The number of nodes in the tree is in the range [1, 1000].   
- `-100 <= Node.val <= 100`


### 작성 코드
```java
/**
 * Definition for a binary tree node.
 * public class TreeNode {
 *     int val;
 *     TreeNode left;
 *     TreeNode right;
 *     TreeNode() {}
 *     TreeNode(int val) { this.val = val; }
 *     TreeNode(int val, TreeNode left, TreeNode right) {
 *         this.val = val;
 *         this.left = left;
 *         this.right = right;
 *     }
 * }
 */
class Solution {
	public boolean isSymmetric(TreeNode root) {
		// 1. 빈 노드가 주어졌다면 참 처리
		if (root == null) return true;
		
		// 2. 반환
		return dfs(root.left, root.right);
	}
	
	/**
	 * DFS
	 */
	public boolean dfs(TreeNode leftChildNode, TreeNode rightChildNode) {
		// 1. 유효성 체크
		if (leftChildNode == null && rightChildNode == null) return true;
		if (leftChildNode == null || rightChildNode == null) return false;
		if (leftChildNode.val != rightChildNode.val) return false;
		
		// 2. DFS 처리
		return dfs(leftChildNode.left, rightChildNode.right) && dfs(leftChildNode.right, rightChildNode.left);
	}
}
```
![](/assets/image/Pasted%20image%2020250528023211.png)


### 회고
- `BFS`와 `DFS` 모두 적용 할 수 있는 경우 어떤 방법이 더욱 적합한 지도 체크해야겠다.