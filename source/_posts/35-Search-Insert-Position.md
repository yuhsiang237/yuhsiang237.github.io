---
title: LeetCode -  35. Search Insert Position
date: 2021-09-21 09:20:34
categories:
  - [Code,C#]
  
  - [Topic,LeetCode]
tag:
    - C#
    - 解題
    - LeetCode
---
<div class="content__u3I1 question-content__JfgR"><div><p>Given a sorted array of distinct integers and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.</p>
<p>You must&nbsp;write an algorithm with&nbsp;<code>O(log n)</code> runtime complexity.</p>
<p>&nbsp;</p>
<p><strong>Example 1:</strong></p>
<pre><strong>Input:</strong> nums = [1,3,5,6], target = 5
<strong>Output:</strong> 2
</pre><p><strong>Example 2:</strong></p>
<pre><strong>Input:</strong> nums = [1,3,5,6], target = 2
<strong>Output:</strong> 1
</pre><p><strong>Example 3:</strong></p>
<pre><strong>Input:</strong> nums = [1,3,5,6], target = 7
<strong>Output:</strong> 4
</pre><p><strong>Example 4:</strong></p>
<pre><strong>Input:</strong> nums = [1,3,5,6], target = 0
<strong>Output:</strong> 0
</pre><p><strong>Example 5:</strong></p>
<pre><strong>Input:</strong> nums = [1], target = 0
<strong>Output:</strong> 0
</pre>
<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>1 &lt;= nums.length &lt;= 10<sup>4</sup></code></li>
	<li><code>-10<sup>4</sup> &lt;= nums[i] &lt;= 10<sup>4</sup></code></li>
	<li><code>nums</code> contains <strong>distinct</strong> values sorted in <strong>ascending</strong> order.</li>
	<li><code>-10<sup>4</sup> &lt;= target &lt;= 10<sup>4</sup></code></li>
</ul>
</div></div>
C# Sol:

```
public class Solution {
    public int SearchInsert(int[] nums, int target) {
        int index = 0;
        Boolean isFind = false;
        for(int i=0;i<nums.Length;i++){
            if(nums[i] == target){
                isFind = true;
                index = i;
                break;
            }
        }
        if(!isFind){
            for(int i=0;i<nums.Length;i++){
                if(nums[i] > target){
                    index = i;
                    break;
                }else if(i == nums.Length -1 ){
                    index = i+1;
                }
            }
        }
        return index;
    }
}
```

Runtime: 92 ms
Memory Usage: 24.9 MB
	