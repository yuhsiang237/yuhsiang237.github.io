---
title: LeetCode - 1. Two Sum
date: 2021-08-23 21:48:10
categories:
  - [程式語言,C#]
  - [程式語言,Javascript]
  - [解題,C#]
  - [解題,Javascript]
  - [解題,LeetCode]
tag:
    - C#
    - JavaScript
    - 解題
    - LeetCode
---
Given an array of integers nums and an integer target, return indices of the two numbers such that they add up to target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

You can return the answer in any order.



Example 1:

Input: nums = [2,7,11,15], target = 9
Output: [0,1]
Output: Because nums[0] + nums[1] == 9, we return [0, 1].

Example 2:

Input: nums = [3,2,4], target = 6
Output: [1,2]

Example 3:

Input: nums = [3,3], target = 6
Output: [0,1]

 

Constraints:

    2 <= nums.length <= 104
    -109 <= nums[i] <= 109
    -109 <= target <= 109
    Only one valid answer exists.

 
Follow-up: Can you come up with an algorithm that is less than O(n^2) time complexity?


**C#解答1**
```
public class Solution {
    public int[] TwoSum(int[] nums, int target) {
        for (int i = 0; i < nums.Length - 1 ; i++)
        {
            for(int j=i+1;j < nums.Length;j++){
                if(nums[i]+nums[j] == target){
                     return new int[] { i,j };
               }
            }
        }
        return new int[]{};
    }
}
```
Runtime: 316 ms, faster than 51.22% of C# online submissions for Two Sum.
Memory Usage: 32.1 MB, less than 96.22% of C# online submissions for Two Sum.


**javascript解答1**
```
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
      for (let i = 0; i < nums.length - 1 ; i++)
        {
            for(let j=i+1;j < nums.length;j++){
                if(nums[i]+nums[j] === target){
                     return [i,j];
               }
            }
        }
        return [];
};
```
Runtime: 116 ms, faster than 35.30% of JavaScript online submissions for Two Sum.
Memory Usage: 39.2 MB, less than 93.93% of JavaScript online submissions for Two Sum.

**思路**
目前解法1是用兩個for迴圈，時間複雜度O(n^2)。
如果有問題歡迎指證!@@

**延伸閱讀**
[[演算法]如何衡量程式的效率？——論時間複雜度（Time Complexity）](https://ithelp.ithome.com.tw/articles/10203082)

