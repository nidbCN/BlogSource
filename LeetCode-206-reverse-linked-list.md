title: '反转链表-力扣206 迭代'
author: Gaein nidb
categories:
  - 代码如诗
cover: "https://s3.ax1x.com/2020/11/12/DSu1dH.png"
tags:
  - 算法
  - 笔记
  - C语言
date: 2020-11-13 00:00:59
---
中北大学 AI+移动互联创新实验室 C语言培训作业，力扣第206题反转链表 题解
<!-- more -->

> 反转链表听起来蛮难的，但是也就是一次迭代改变链表中指针的指向，将其反向指回去。这种方法比复制一个链表出来效率要高。当然，作为单向链表来说，去复制链表也不是什么简单的好办法。

# 题干


[[力扣-反转链表](https://leetcode-cn.com/problems/reverse-linked-list/)]

> 反转一个单链表。  
> 示例:
> ```
> 输入: 1->2->3->4->5->NULL
> 输出: 5->4->3->2->1->NULL
> ```
> 进阶:
> 你可以迭代或递归地反转链表。你能否用两种方法解决这道题？

# 迭代

## 思路
 
说实话迭代是什么也不是很理解，廖雪峰有一篇文章介绍了迭代：[[廖雪峰的官网](https://www.liaoxuefeng.com/wiki/1016959663602400/1017316949097888)] 大致上可以理解为遍历吧。大致过程就是不断的改变指针指向，将本节点指向下一个节点的指针指向上一个节点，因此需要临时的结构体指针来保存节点（毕竟是单向链表，不能回去）。为了方便理解，粗略的画了一下图，在实际应用中关于链表的问题画图确实比较好理解一些。（u1s1，surface的笔是真的爽，买电脑不能只看性能啊，另外字太丑了...）

![图一](https://s3.ax1x.com/2020/11/12/DSu1dH.png)
在开始的时候，定义临时节点`nextNode`，然后更改`head->next`指向`NULL`，然后定义临时节点`lastNode`用于保存上一节点，将`head`指向`nextNode`  

![图二](https://s3.ax1x.com/2020/11/12/DSuMLD.png)
循环，修改`nextNode`、`lastNode`、`head`等指针的指向，不断的将`节点->next`反转，并向前移动`head`

![图三](https://s3.ax1x.com/2020/11/12/DSulee.png)
当`head->next == NULL`的时候即为最后一个节点，此时结束循环并修改最后一个节点的指向，然后返回链表。

## 代码

```c
/**
 * 单向链表的结构体定义
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */


struct ListNode *reverseList(struct ListNode *head)
{
    struct ListNode *lastNode = head;
    struct ListNode *temp = head;

    if (head != NULL && head->next != NULL)
    {
        for (int isFirst = 1; head->next != NULL; temp = head)
        {
            head = head->next;
            if (isFirst == 1)
            {
                temp->next = NULL;
                isFirst = 0;
            }
            else
            {
                temp->next = lastNode;
            }
            lastNode = temp;
        }
        head->next = lastNode;
    }
    return head;
}
```

这次只使用了C语言，因为C#反转链表应该不难，`list.Reverse();`（逃）

## 结果

仅有一次循环，使用迭代的方法还是蛮快的，完成了力扣的任务。但是递归我实在不会，甚至不能很好的区分它们。所以暂时就没有写递归方法。
![力扣](https://s3.ax1x.com/2020/11/12/DSKJ74.md.jpg)

# 最后

这道题的话还是比较简单的，毕竟画起图来感觉不难，但是实际写起来还是有一定挑战性的。通过这道题顺手复习了忘干净的链表，也是非常好的。链表的题先画图理解一下有很大帮助，尤其是自己用笔手动遍历一下，利于理解过程。