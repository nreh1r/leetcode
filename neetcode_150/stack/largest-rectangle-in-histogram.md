---
tag: code_problem
time_elapsed: 11
difficulty: hard
category: stack
created: 2025-12-14T21:00
updated: 2025-12-14T21:00
---

# 84. Largest Rectangle in Histogram

**Notes**

I was able to get the brute force solution. The optimal solution is still a work in progress.

***Complexity Analysis***

The brute force solution is quite inefficient at `O(n^2)` time complexity. This is because the brute force solution employs a nested for loop going over all possible rectangle combinations in the input array. The space complexity however is `O(1)` as no additional memory is needed as the input grows (there are just tracking variables used).

```python
class Solution:
    def largestRectangleArea(self, heights: List[int]) -> int:
        max_area = 0
        l = 0
        while l < len(heights):
            min_height = heights[l]
            curr_max_area = heights[l]
            for r in range(l + 1, len(heights)):
                min_height = min(min_height, heights[r])
                curr_max_area = max(curr_max_area, min_height * (r - l + 1))
            max_area = max(max_area, curr_max_area)
            l += 1
        return max_area
```