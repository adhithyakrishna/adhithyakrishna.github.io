---
title: "Squares of a sorted array"
description: "Solution for Leetcode 977"
date: 2021-01-13T03:30:51+00:00
draft: false
weight: 1
---

## Solution for leetcode 977
{{< tabs Golang Java >}}

{{< tab >}}
### Golang solution
```Go
func sortedSquares(nums []int) []int {
    result := make([]int, len(nums))
    
    start := 0
    end := len(nums)-1
    index := len(nums)-1
    
    for start <= end {
        if(abs(nums[start]) >= abs(nums[end])) {
            result[index] = nums[start] * nums[start]
            start++
        } else {
            result[index] = nums[end] * nums[end]
            end--
        }
        index--
    }
    return result
}

func abs(x int) int {
    
    if(x < 0) {
        return x * -1;
    }
    
    return x
}
```
{{< /tab >}}

{{< tab >}}
### Java solution
```Java
class Solution {
    public int[] sortedSquares(int[] nums) {
        int[] result = new int[nums.length];
        
        int start = 0;
        int end = nums.length - 1;
        int index = nums.length - 1;
        while(start <= end) {
            if(abs(nums[start]) <= abs(nums[end])) {
                result[index] = nums[end] * nums[end];
                end--;
            }
            else {
                result[index] = nums[start] * nums[start];
                start++;
            }
            index--;
        }
        
        return result;
    }
    
    public int abs(int data)
    {
        return data < 0 ? data * -1 : data;
    }
}
```
{{< /tab >}}
{{< /tabs >}}

{{< box >}}
Time complexity : O(N)
Space complexity : O(N)
{{< /box >}}