---
layout: single
title: LeetCode 1161. Maximum Level Sum of a Binary Tree
categories: CodingTest
tags: [CodingTest]
toc: true
sidebar:
    nav: "counts"
---

# 📊 LeetCode 1161. Maximum Level Sum of a Binary Tree

## 📝 문제 설명

바위트리가 주어진다. 만약 이 바위트리의 각 레벨에 소수의 합이 있다면, 가장 크게 된 합을 가지는 레벨을 찾아서 구해라.

레벨 1은 루트에 해당한다. 역시 레벨 2는 루트의 능력가 없는 왼쪽과 오른쪽 노드에 이어진다.

어느 레벨의 정수의 합이 가장 크면, 그 레벨 번호를 보내라. (루트는 레벨 1)

---

## 🔍 가지보기

키 해결 방법은 **BFS (Breadth-First Search)** 를 이용하여 레벨 별 획사 가능.

1. 루트를 귀의로 하여 queue에 추가
2. 레벨을 순서대로 호출하며 각 레벨의 노드 각 각을 찾아 queue에 다음 레벨으로 추가
3. 이 시스템을 이용하여 각 레벨의 정수 합 계산
4. 가장 크는 합을 가지는 레벨 저장

---

## ✅ Python 코드

```python
from collections import deque

class Solution:
    def maxLevelSum(self, root: Optional[TreeNode]) -> int:
        q = deque([root])
        max_sum = float('-inf')
        answer_level = 1
        level = 1

        while q:
            level_sum = 0
            for _ in range(len(q)):
                node = q.popleft()
                level_sum += node.val
                if node.left:
                    q.append(node.left)
                if node.right:
                    q.append(node.right)

            if level_sum > max_sum:
                max_sum = level_sum
                answer_level = level

            level += 1

        return answer_level
```

---

## ⏱ 시간본사도

- **시간본사도:** `O(N)`  — 노드 개수가 N개이면 하나씩 팔린다
- **공간본사도:** `O(W)`  — 각 레벨에서 모아진 가상 목록의 개수 (W = 레벨 노드 개수 가상)

---

## ✅ 정리

- 레벨 별 탐색은 갈알적으로 **BFS**가 더 현실적
- 루트 에서 시작해 queue로 각 레벨 팔리기
- 각 레벨의 합을 계산해가며 가장 크는 값을 찾으며 레벨 번호 저장

---

## 🔗 문제 링크

- [LeetCode 1161. Maximum Level Sum of a Binary Tree](https://leetcode.com/problems/maximum-level-sum-of-a-binary-tree/)

