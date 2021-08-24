---
title: 單向鏈結串列(Singly linked list)
date: 2021-08-24 11:15:05
tags:
---

  
  
**整理**
{% asset_img Singly-linked-list.svg.png 單向鏈結串列 %}

**<center>單向鏈結串列(Singly linked list)示意圖</center>**

**每一個節點內含**
next  // 儲存下一個的節點
val // 數值

如果該節點next為null代表已經沒有下個節點串接。

如上圖範例:
數值為12節點的下個節點(next)，接數值為99節點
數值為99節點的下個節點(next)，接數值為37節點
而數值37節點的下個節點(next)，表示沒有結點串接為null

**Big-O**
search	搜尋	O(N)

**特點**
只能做循序存取 (Sequential access)


**C#程式碼**

```
using System;

//節點
public class LinkNode
{
   public int val;
   public LinkNode next;
   public LinkNode(int val,LinkNode next){
	   this.val = val;
	   this.next = next;
   }
   public LinkNode(int val){
	   this.val = val;
	   this.next = null;
   }
};

public class Program
{
	// SingleLinkList 串接節點
	public static void Main()
	{
	
		LinkNode node1 = new LinkNode(12); 
		LinkNode node2 = new LinkNode(99); 
		LinkNode node3 = new LinkNode(37); 
		
		node1.next = node2; // 綁定node1的尾端接node2
		node2.next = node3; // 綁定node2的尾端接node3
		
	
		Console.WriteLine("手動印出節點:");
		Console.WriteLine(node1.val); // 節點1
		Console.WriteLine(node1.next.val);// 節點2 (節點1的下1個元素)
		Console.WriteLine(node1.next.next.val);// 節點3 (節點1的下2個元素)
		if(node1.next.next.next == null){ //節點4，尾端沒串接任何資料為null
			Console.WriteLine("null");
		}
		
		Console.WriteLine("遍歷節點:");
		LinkNode currentNode = node1;
		while(currentNode != null){ // null表示為末端
			Console.WriteLine(currentNode.val);
			// 將當前節點指到下個節點
			currentNode = currentNode.next;
		}
		
	}
}
```

**結果:**

```
手動印出節點:
12
99
37
null
遍歷節點:
12
99
37
```

**參考資料**
https://kopu.chat/2017/05/30/c-%e8%aa%9e%e8%a8%80%ef%bc%9a%e7%b5%90%e6%a7%8b%e8%ae%8a%e6%95%b8%e8%88%87%e6%8c%87%e6%a8%99/
https://en.wikipedia.org/wiki/Linked_list
https://zh.wikipedia.org/wiki/%E9%93%BE%E8%A1%A8
