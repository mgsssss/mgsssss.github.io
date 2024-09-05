---
layout: single
title: 695. Max Area of Island
categories: CodingTest
tags: [CodingTest]
toc: true
---

# 695. Max Area of Island

이번 문제는 DFS or BFS 둘다 풀이가 가능한 문제이다.
해당 문제는 Acceptance Rate가 72.5%로 상당히 높다.
---
### 문제 설명
    You are given an m x n binary matrix grid. An island is a group of 1's (representing land) connected 4-directionally (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.

    The area of an island is the number of cells with a value 1 in the island.

    Return the maximum area of an island in grid. If there is no island, return 0.

문제 설명을 해석 하자면 m X n의 이진 행렬이 주어지고, 해당 grid 에서 0은 물 1은 육지로 되어있고, 섬은 4방향 (수평 또는 수직)으로 구성되어 있다.
섬의 최대 면적을 반환하고, 섬이 없다면 0을 반환한다.

----

## 풀이법

```python

# Time complexity: O(m * n)
# Space complexity: O(m * n)

from collections import deque
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        rows = len(grid) # 행의 길이
        cols = len(grid[0]) # 열의 길이

        # 방문 여부, 중복 방문 제한
        visited = [[0] * cols for _ in range(rows)]
        # 최대 섬의 갯수
        cnt = 0

        # 위, 아래, 양옆
        delta = [(0,1), (1,0), (-1,0), (0,-1)]
        
        def bfs(row, col):
            visited[row][col] = 1
            q = deque([(row, col)])
            cnt = 1
            while q:
                
                cur_row, cur_col = q.popleft()

                for dr, dc in delta:
                    next_row = cur_row + dr
                    next_col = cur_col + dc

                    if next_row < rows and next_col < cols and next_row >= 0 and next_col >= 0:
                        if visited[next_row][next_col] == 0 and grid[next_row][next_col] == 1:
                            q.append( (next_row, next_col) )
                            visited[next_row][next_col] = 1
                            cnt += 1
            return cnt

        
        for row in range(0, rows):
            for col in range(0, cols):
                if visited[row][col] == 0 and grid[row][col] == 1:
                    cnt =  max(bfs(row, col), cnt)
        return cnt
```