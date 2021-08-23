---
title: LeetCode - 2. add-two-number
date: 2021-08-24 04:09:34
categories:
  - [程式語言,C#]
  - [解題,C#]
  - [解題,LeetCode]
tag:
    - C#
    - 解題
    - LeetCode
---
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order, and each of their nodes contains a single digit. Add the two numbers and return the sum as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

 

Example 1:

{% asset_img addtwonumber1.jpg 題目 %}

Input: l1 = [2,4,3], l2 = [5,6,4]
Output: [7,0,8]
Explanation: 342 + 465 = 807.

Example 2:

Input: l1 = [0], l2 = [0]
Output: [0]

Example 3:

Input: l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
Output: [8,9,9,9,0,0,0,1]

 

Constraints:

    The number of nodes in each linked list is in the range [1, 100].
    0 <= Node.val <= 9
    It is guaranteed that the list represents a number that does not have leading zeros.

**C#解答1**
```
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     public int val;
 *     public ListNode next;
 *     public ListNode(int val=0, ListNode next=null) {
 *         this.val = val;
 *         this.next = next;
 *     }
 * }
 */

public class Solution {
  
    public ListNode AddTwoNumbers(ListNode l1, ListNode l2) {
        ListNode currentL1=l1,currentL2=l2;
        ListNode resultListNode = new ListNode();
        ListNode resultList = resultListNode; // 為什麼可以儲存?
        Boolean isAddOne = false;
        while(true){
            int sum = 0;
            if(currentL1!= null){
                sum +=  currentL1.val;
                currentL1 = currentL1.next;
            }
            if(currentL2!= null){
              sum +=  currentL2.val;
              currentL2 = currentL2.next;
            }
            
            int result = sum;

            if(isAddOne){
                sum+=1;
            }
            
            if(sum >= 10){
                isAddOne = true;
                sum = sum%10; 
            }else{
                isAddOne = false;
            }
            
            resultListNode.next = new ListNode(sum); 
            resultListNode = resultListNode.next; // ??
            
            if(currentL1 == null && currentL2 == null && isAddOne == false){
                break;
            }
        }
        return resultList.next;
    }
}
```
Runtime: 96 ms, faster than 97.02% of C# online submissions for Add Two Numbers.
Memory Usage: 28.2 MB, less than 82.24% of C# online submissions for Add Two Numbers.

**思路**
&nbsp;&nbsp;這題是中級題目，但對我來說其實已經蠻吃力了，在進位出807這段其實很容易，但後面那段linknode塞進去時卡很久(註解那部分)，最後還是上網查了一下，資料結構真的要熟悉，以及"抽象"思考的方式，目前就是要拿筆整理，腦中還是很難構造出，就是一種組合的概念。
&nbsp;&nbsp;這也讓我認清，中階工程師跟語法工程師是天壤之別。