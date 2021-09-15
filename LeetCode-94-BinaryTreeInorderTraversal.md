title: '二叉树的中序遍历-力扣94 中序遍历'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 算法
  - 笔记
  - CSharp
date: 2021-09-15 22:22:13
---

## 题干

[力扣-二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)]

> 题干详见 LeetCode ，图片过多不在此赘述。

## 递归

#### 思路

首先分析题干，迷惑了我半天的示例原来是这个类：

```csharp
public class TreeNode {
    public int val;
    public TreeNode left;
    public TreeNode right;
    public TreeNode(int val = 0 , TreeNode left = null, TreeNode right = null) {
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

> 不得不说这一股子 Java 味的 C# 弄得我是...血压升高又无话可说

```csharp
public int Val { get; set;}
public TreeNode Left { get; set;}
public TreeNode Right { get; set;}

public TreeNode(int val = 0 , TreeNode left = null, TreeNode right = null) {
    Val = val;
    Left = left;
    Right = right;
}
```

另外这种构造函数似乎没有必要写，因为可以使用类似于：

```csharp
var node = new TreeNode()
{
    Val = 5,
    Left = chileLeft,
};
```

这种代码来完成。一般情况下用构造函数是属性不可为空的情况下。

> 跑题了（）

首先啥是二叉树，这玩意离散数学应该讲过，~~但是我没听（x）~~ 二叉树是一个树形结构，每个节点有两个子节点。中序遍历就是先遍历左面的，再来他本身然后是右面的。递归的话不深究他的细节，写就完了。

首先来个判空避免 `NullReferenceException` 