title: '两数之和-力扣1 暴力算法/哈希表'
author: Gaein nidb
categories:
  - 代码如诗
cover: "https://s1.ax1x.com/2020/11/10/BbBr8A.md.png"
tags:
  - 算法
  - 笔记
  - C语言
date: 2020-11-9 12:29:51
---
中北大学 AI+移动互联创新实验室 C语言培训作业，力扣第一题两数之和 题解
<!-- more -->

> 其实两数之和这道题在一个月前用Java写过一次，难度应该是不算高。但是力扣判题变得丧心病狂了，一个月前通过了的相同代码到现在就疯狂超时，所以还要看看这题。

## 题干

[[力扣-两数之和](https://leetcode-cn.com/problems/two-sum/)]

>给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那**两个**整数，并返回他们的数组下标。你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。  
> 示例:
> ```
> 给定 nums = [2, 7, 11, 15], target = 9
> 因为 nums[0] + nums[1] = 2 + 7 = 9
> 所以返回 [0, 1]
> ```

## 暴力解法

#### 思路
 
用最暴力的算法两层循环还是非常简单的，只需要让第二层从`i + 1`开始循环即可防止重复，匹配到后将数组下标`i`和`j`添加到返回的数组里面即可

#### 代码

###### Java

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] result = new int[2];    // 临时储存结果
        for (int i = 0; i < nums.length; i++) {     // 循环第一层
            for (int j = i + 1; j < nums.length; j++) {     // 循环第二层
                // j从i+1开始，即不重复加
                if (nums[i] + nums[j] == target) {
                    result[0] = i;
                    result[1] = j;
                    // 将结果的索引存储在result数组中
                    return result;
                }
            }
        }
    }
}
```

###### CSharp(Microsoft Java)

```cs
public class Solution {
    public int[] TwoSum(int[] nums, int target) {
        int[] result = new int[2];    // 临时储存结果

        for (int i = 0; i < nums.Length; i++) {     // 循环第一层
            for (int j = i + 1; j < nums.Length; j++) {     // 循环第二层
                // j从i+1开始，即不重复加
                if (nums[i] + nums[j] == target) {
                    result[0] = i;
                    result[1] = j;
                    // 将结果的索引存储在result数组中
                    return result;
                }
            }
        }
    }
}
```

###### C

```c
int *twoSum(int *nums, int numsSize, int target, int *returnSize)
{
    int *result = NULL; // 定义空指针用来存储返回数据
    *returnSize = 0;    // 默认的返回大小为0
    for (int i = 0; i < numsSize - 1; i++)
    {   // 循环第一层
        for (int j = i + 1; j < numsSize; j++)
        {   // 循环第二层
            if (nums[i] + nums[j] == target)
            {
                result = (int *)malloc(sizeof(int) * 2);  // 为返回结果的指针分配内存
                *result = i;      // 赋值result
                result[1] = j;    // 赋值第二项
                *returnSize = 2;  // 设置返回大小
                goto end;         // 跳出两层循环
            }
        }
    }
end:
    return result;
}
```
p.s.别骂我的goto，使用goto跳出C的两层循环还是蛮方便（逃）

#### 结果

![一个月前的Java暴力算法](https://s1.ax1x.com/2020/11/10/BbBDCd.png)
> 一个月前的Java代码，那个时候还能通过，现在提交同样的代码也会超时

![C语言暴力算法](https://s1.ax1x.com/2020/11/10/BbB04H.png)
> C语言暴力算法结果（超时）

## 哈希表法

#### 思路
> 这是实验室巨佬和我说的，所以我去了解了一下这个思路。大概的实现是这样的：遍历一次数组将值存入哈希表中，同时检查哈希表中是否存在 `target - num` 存在和话返回下标，不存在继续循环并将当前插入哈希表，直到循环结束则无结果。\
官方题解使用的HashMap是开源项目 [uthash](https://troydhanson.github.io/uthash/) leetcode已经默认包含其头文件，可以直接使用（官方文档： https://troydhanson.github.io/uthash/userguide.html）

#### 代码
  
p.s. 写起来还是有点自闭的，现在已经是12:35了，才刚通过一个  
p.s. 代码很长，有很多被注释掉的是当时测试用的，不舍得删掉了(最后还是删了)
```C
/*AI+移动互联创新实验室 C语言培训练习题 两数之和 | 力扣-1*/
##include <stdio.h>
##include <malloc.h>
##include <math.h>
##include <stdbool.h>
// 头文件

##include "uthash\uthash.h"

##define DEFAULT_SIZE 0
##define FOUND_SIZE 2
##define INT2_SIZE sizeof(int) * 2
// 定义常量 PART1

typedef struct _hashMap
{
    int key;
    int val;
    UT_hash_handle hh;
} HashMap;
// 哈希表结构体

##define HASH_SIZE sizeof(HashMap) * 2
// 定义常量 PART2

int *twoSum(int *nums, int numsSize, int target, int *returnSize);
int hashFind(int key, HashMap *in);
bool hashInsert(int key, int val);
// 函数声明

HashMap *numHash = NULL;
// 新哈希表

int *twoSum(int *nums, int numsSize, int target, int *returnSize)
{
    int *ret = NULL;
    *returnSize = DEFAULT_SIZE;

    for (int i = 0; i < numsSize; i++, nums++)
    {
        int other = target - *nums;           // 计算另一个orher
        int index = hashFind(other, numHash); // 将other作为键寻找
        if (index != -1)
        {
            // 找到则进行返回
            ret = (int *)malloc(INT2_SIZE);
            *ret = index;
            ret[1] = i;
            // printf("%d %d\n",index,i );
            *returnSize = FOUND_SIZE;
            break;
        }
        else
        {
            // 未找到则插入数据
            hashInsert(*nums, i); // 插入数字作为键，i作为值
        }
    }
    numHash = NULL;
    // 置空哈希表，因为在力扣上面是多次运行，而哈希表会保留上次运行的数据
    return ret;

// 哈希表查找值
int hashFind(int key, HashMap *in)
{
    HashMap *tmp = NULL;
    HASH_FIND_INT(in, &key, tmp);
    int ret = -1;
    if (tmp != NULL)
    {
        ret = tmp->val;
    }
    return ret;
}

// 哈希表插入值
bool hashInsert(int key, int val)
{
    bool ret = false;
    int it = -1;

    if (HASH_COUNT(numHash) != 0)
    {
        it = hashFind(key, numHash);
    }
    if (it == -1)
    {
        HashMap *newHash;
        newHash = malloc(HASH_SIZE);
        newHash->val = val;
        newHash->key = key;
        HASH_ADD_INT(numHash, key, newHash);
        // free(newHash); // 加上这一行会爆炸
        ret = true;
    }
    return ret;
}
```

#### 结果
![C语言代码](https://s1.ax1x.com/2020/11/10/BbBwUe.png)
> C语言的代码，时间还算不错，内存占用大了一点

## 最后

说起这题来真是心酸啊，跨越了一个月，也通过这道题学(zì)会(bì)了很多
![通过截屏](https://s1.ax1x.com/2020/11/10/BbBr8A.png)
![艰辛历史](https://s1.ax1x.com/2020/11/10/BbDKMt.png)