---
title: 💡 LeetCode 21 - Merge Two Sorted Lists
date: 2025-02-22 23:33:00 +0900
categories:
  - Problem-Solving
tags:
  - Problem-Solving
  - LeetCode
  - EASY
---

### 문제
> You are given the heads of two sorted linked lists list1 and list2.  
> Merge the two lists into one sorted list. The list should be made by splicing together the nodes of the first two lists.  
> Return the head of the merged linked list.


### 입출력 예제
#### ✅ 예제 1
```bash
Input: list1 = [1,2,4], list2 = [1,3,4]
Output: [1,1,2,3,4,4]
```

#### ✅ 예제 2
```bash
Input: list1 = [], list2 = []
Output: []
```

#### ✅ 예제 3
```bash
Input: list1 = [], list2 = [0]
Output: [0]
```


### 제약조건
- `The number of nodes in both lists is in the range [0, 50].
- `-100 <= Node.val <= 100`
- Both list1 and list2 are sorted in non-decreasing order.


### 작성 코드
```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
	public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
		// 1. 변수 선언 및 초기화
		ListNode resultNode = new ListNode();
		ListNode tempNode = resultNode;
		
		// 2. 리스트에 결과 순서대로 추가
		while (list1 != null || list2 != null) {
			if (list1 == null) {
				tempNode.next = list2;
				list2 = list2.next;
			} else if (list2 == null) {
				tempNode.next = list1;
				list1 = list1.next;
			} else {
				if (list1.val <= list2.val) {
					tempNode.next = list1;
					list1 = list1.next;
				} else {
					tempNode.next = list2;
					list2 = list2.next;
				}
			}
			tempNode = tempNode.next;    
		}    
		
		// 3. 반환
		return resultNode.next;
	}
}
```
![](/assets/image/Pasted%20image%2020250528003044.png)
- `ListNode`는 문제에서 주어지는 클래스로, 주석을 통해 어떻게 작성되어 있는지 알 수 있었다.
- 주어진 파라미터가 문제에서 주어진 `ListNode` 인스턴스가 아닌 배열이나 `List`였다면 `Deque`를 사용할 수 있었을 것 같다.


### 회고
- 참조 개념을 정리하며 로컬 변수는 `Stack` 메모리에, 인스턴스는 `Heap` 메모리에 생성된다는 점을 알게 되었다.
- 인스턴스는 메서드 실행 중간에 `null`을 참조하더라도 바로 사라지지 않고, 메서드가 종료되면 메모리 상에서 없어진다고 한다.