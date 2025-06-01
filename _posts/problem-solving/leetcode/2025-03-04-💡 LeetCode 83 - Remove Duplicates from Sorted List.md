---
title: 💡 LeetCode 83 - Remove Duplicates from Sorted List
date: 2025-03-04 01:26:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
>Given the head of a sorted linked list, delete all duplicates such that each element appears only once.   
>
>Return the linked list sorted as well.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: head = [1,1,2]
Output: [1,2]
```

#### ✅ 예제 2
```bash
Input: head = [1,1,2,3,3]
Output: [1,2,3]
```


### 제약조건
- The number of nodes in the list is in the range [0, 300].  
- `-100 <= Node.val <= 100`
- The list is guaranteed to be sorted in ascending order.


### 작성 코드
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
	public ListNode deleteDuplicates(ListNode head) {
		// 1. 변수 선언 및 초기화
		ListNode resultNode = new ListNode();
		ListNode tempNode = resultNode; 
		
		// 2. 유효성 체크 1 : head가 null일 경우
		if (head == null) return null;
		
		// 3. 유효성 체크 2 : head.next가 null일 경우
		if (head.next == null) return head;
		
		// 4. 순회하며 결과 노드 생성
		while (head != null && head.next != null) {
			if (head.val == head.next.val) {
				// 현 노드와 다음 노드 값이 같을 경우 다음 노드 건너뛰기
				tempNode.next = head.next;
			} else {
				// 현 노드와 다음 노드 값이 다를 경우 결과 노드에 추가 후 이동
				tempNode.next = head;
				tempNode = tempNode.next;
			}
			// head 이동
			head = head.next;
		}
		
		// 5. 반환
		return resultNode.next;
	}
}
```
![](/assets/image/Pasted%20image%2020250528021517.png)