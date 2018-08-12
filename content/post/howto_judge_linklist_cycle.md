+++
banner = ""
categories = ["Programming"]
date = "2016-11-21T17:22:51+08:00"
description = ""
images = []
menu = ""
tags = ["algorithm"]
title = "如何判断单链表有环"
+++

从自己的CSDN BLOG移过来的文章，作个BACKUP:

#### 1、对于单链表而言，只有通过从头到尾的遍历方式，如果发现尾指针指向头指针则说明有环：

```
bool hasCycle(struct ListNode *head)
{
	if(head == NULL || head->next == NULL){
		return false;
	}
	
	struct ListNode *ptr = head->next;
	
	while(ptr)
	{
		if (ptr->next == head){
			return true;
		}	
		
		if (ptr->next == NULL)
			break;
			
		ptr = ptr->next;	
	}
	
	return false;
}
```

#### 2、使用双指针，一个遍历的速度快，一个较慢，如果快的指针追上了慢的那个说明有环：

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
bool hasCycle(struct ListNode *head) 
{
	struct ListNode *faster = NULL;
	struct ListNode *slower = NULL;
	
	if(head == NULL || head->next == NULL){
		return false;
	}
	
	slower = head;
	faster = head->next;
	while(faster != slower){
		if(faster == NULL || faster->next == NULL)
		{
			return false;
		}
		
		faster = faster->next->next;
		slower = slower->next;
	}
	
	return true;
}
```


