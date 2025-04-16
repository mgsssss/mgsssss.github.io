---
layout: single
title: LeetCode 875. Koko Eating Bananas
categories: CodingTest
tags: [CodingTest]
toc: true
sidebar:
    nav: "counts"
---

# 🍌 LeetCode 875. Koko Eating Bananas

## 📝 문제 설명

코코는 바나나를 정말 좋아한다.  
`piles` 배열에는 바나나 더미가 주어진다. 코코는 **1시간에 정해진 속도 K**로 바나나를 먹는다.  
**한 시간에 하나의 더미만 먹을 수 있고**, 각 더미에서 원하는 만큼 먹을 수 있다.  
총 `H` 시간 안에 모든 바나나를 먹을 수 있는 **최소의 K를 구해라.**

---

## 🔍 예시

```python
piles = [3, 6, 7, 11]
H = 8
# 출력: 4
```

속도 K = 4일 때 걸린 시간:  
3 → 1시간, 6 → 2시간, 7 → 2시간, 11 → 3시간 → 총 8시간

---

## 💡 가지보기

이 문제는 **Binary Search on Answer (정답 범위를 이진 탐색)** 형태의 전형적인 문제다.

- 가능한 속도 K의 범위는 `1 ~ max(piles)`
- 어느 속도 `K`가 주어진다고 할 때, 그 속도로 **H시간 안에 모든 바나나를 다 먹을 수 있는지** 판단하는 함수 `can_finish(K)`를 만들다.
- 이분 탐색으로 가능한 최소 K를 찾는다.

---

## ✅ Python 코드

```python
import math
from typing import List

class Solution:
    def minEatingSpeed(self, piles: List[int], h: int) -> int:
        def can_finish(speed):
            time = 0
            for pile in piles:
                time += math.ceil(pile / speed)
            return time <= h

        left, right = 1, max(piles)
        answer = right

        while left <= right:
            mid = (left + right) // 2
            if can_finish(mid):
                answer = mid
                right = mid - 1
            else:
                left = mid + 1

        return answer
```

---

## ⏱ 시간본사도

- **시간본사도:** `O(N * logM)`  
  - `N` = 바나나 더미 수  
  - `M` = `max(piles)`  

- **공간본사도:** `O(1)` — 추가 메리 없음

---

## ✅ 정리

- 정답이 정수 범위에 있을 때 사용할 수 있는 **Binary Search on Answer**의 대표 문제
- `can_finish()`를 통해 "이 속도면 가능한가?" 를 판단하는 **결정 문제**로 변화한 것이 해결 키
- 이진 탐색을 정답 찾기에 적용하는 생각의 방식을 연습할 수 있다

---

## 🔗 문제 링크

- [LeetCode 875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)

