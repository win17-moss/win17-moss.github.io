---
layout:     post
title:      leetcode Day1
subtitle:   使用堆排序实现快速重组新链表
date:       2018-06-06
author:     Landry
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Python
    - 算法
    - Leetcode
---
# 前言

>怕什么技术无边，进一步有进一步的欢喜
    堆排序作为一种查询最值表现很好的排序方法，在时间复杂度上表现尤为突出，以入门的python已经把堆排序方法打包成了几个函数，非常容易使用
# 常见操作方法介绍
堆是一种很好用的数据结构（heap），是一种天生的优先队列。

但是python并没有独立的堆类型，只有一个包含堆操作的模块（heapq）：heap qury

heapq 有六个函数如下：

heappush(heap,x)： 将x存入堆内

heappop(heap): 提出最小值

heapify(list1):让列表堆化

heapreplace(heap,x) 剔除最小元素，将x存入堆

nlargest(n,iter),nsmallest(n,iter)
返回iter中 n 个最大（最小）元素

最后两种方法相对于排序后再切片，使用的时间跟空间都少很多

#### 操作须知
heappush 的对象一定是heap本身并不能赋值给另外一个变量，如果强行赋值，就会赋空值，是添加操作还是可以成功
python里没有堆这样的定义类，所谓的堆只是有堆性质的list

```
题目：合并 k 个排序链表，返回合并后的排序链表。请分析和描述算法的复杂度。


实例:
 输入：
 [
 
  1->4->5,
  
  1->3->4,
  
  2->6
  
]

输出: 1->1->2->3->4->4->5->6


```python
from typing import List

class ListNode():
    def __init__(self, x):
        self.val = x # 节点值
        self.next = None

        
class Solution:
    def mergeKLists(self, lists: List[ListNode]) -> ListNode:
        if not lists or len(lists) == 0:
            return None

        heap = []
        # 首先 for 嵌套 while 就是将所有元素都取出放入堆中
        for node in lists:
            while node:
                heapq.heappush(heap, node.val)
                node = node.next
        dummy = ListNode(None)
        cur = dummy
        # 依次将堆中的元素取出(因为是小顶堆，所以每次出来的都是目前堆中值最小的元素），然后重新构建一个列表返回
        while heap:
            temp_node = ListNode(heapq.heappop(heap))
            cur.next = temp_node
            cur = temp_node
        return dummy.next

```


```python
data11 = ListNode(1)
data12 = ListNode(4)
data11.next = data12

data13 = ListNode(5)
data12.next = data13


data21 = ListNode(1)
data22 = ListNode(3)
data21.next = data22

data23 = ListNode(4)
data22.next = data23

data31 = ListNode(7)
data32 = ListNode(9)
data31.next = data32

data_list = [data11,data21,data31]
```


```python
class Solution:
    def mergeKLists(self,lists:List[ListNode]) -> ListNode:
        if len(lists)==0 or lists == None:
            return None
        heap = []
        for node in lists:
            while node:
                heapq.heappush(heap,node.val)
                node = node.next
        dummy = ListNode(None)
        cur = dummy
        while heap:
            node_temp = ListNode(heapq.heappop(heap))
            cur.next = node_temp
            cur = node_temp
        return dummy.next
            
        
```


```python
sol = Solution()
x = sol.mergeKLists(data_list)
while x:
    print(x.val)
    x = x.next
```

    1
    1
    3
    4
    4
    5
    7
    9



```python
summary:
    用堆的特性去解决排序问题，一般来说会省空间时间，但是这个具体问题因为要存成链表格式，所以有一个list转Listnode的过程。
    
    type包里 有可以定义具体内容的List
```