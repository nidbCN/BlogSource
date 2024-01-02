---
title: '二叉树的中序遍历-力扣94 中序遍历'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 算法
  - 笔记
  - CSharp
date: 2021-09-17 18:50:22
---

## 题干

[力扣-最大正方形](https://leetcode-cn.com/problems/maximal-square/)

> 题干详见 LeetCode ，图片过多不在此赘述。

## 暴力

#### 思路

暴力算嘛，计算每一个最大的正方形的边长并存储，遍历一轮。

#### 代码

```csharp
public int MaximalSquare(char[][] matrix)
{
    var maxSide = 0;

    // 判空
    if (matrix is null || matrix.Length == 0 || matrix[0].Length == 0)
        return 0;

    var rows = matrix.Length;
    var columns = matrix[0].Length;

    for (var i = 0; i < rows; i++)
    {
        for (var j = 0; j < columns; j++)
        {
            if (matrix[i][j] == '1')
            {
                // 遇到一个 1 作为正方形的左上角
                maxSide = maxSide > 1 ? maxSide : 1;

                // 计算可能的最大正方形边长
                var currentMaxSide = rows - i > columns - j ? columns - j : rows - i;
                for (var k = 1; k < currentMaxSide; k++)
                {
                    // 判断新增的一行一列是否均为 1
                    var flag = true;
                    if (matrix[i + k][j + k] == '0')
                        break;

                    for (var m = 0; m < k; m++)
                    {
                        if (matrix[i + k][j + m] == '0' || matrix[i + m][j + k] == '0')
                        {
                            flag = false;
                            break;
                        }
                    }

                    if (flag)
                    {
                        maxSide = maxSide > k + 1 ? maxSide : k + 1;
                    }
                    else
                    {
                        break;
                    }
                }
            }
        }
    }

    return maxSide * maxSide;
}
```

