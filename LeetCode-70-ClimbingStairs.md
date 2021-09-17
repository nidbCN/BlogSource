title: '爬楼梯-力扣70 动态规划'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 算法
  - 笔记
  - CSharp
date: 2021-09-14 21:24:43
---

> 嗯是爬楼梯，刚看题没有什么思路，举几个栗子来看看规律，找到规律后使用规律来阶梯。

## 题干

[[力扣-爬楼梯](https://leetcode-cn.com/problems/climbing-stairs/)]

> 假设你正在爬楼梯。需要 n 阶你才能到达楼顶。  
> 每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？  
> **注意：** 给定 n 是一个正整数。  
> 示例 1：
> ```  
> 输入： 2  
> 输出： 2
> 解释： 有两种方法可以爬到楼顶。
> 1.  1 阶 + 1 阶
> 2.  2 阶
> ```  
> 示例 2：
> ```
> 输入： 3
> 输出： 3
> 解释： 有三种方法可以爬到楼顶。
> 1.  1 阶 + 1 阶 + 1 阶
> 2.  1 阶 + 2 阶
> 3.  2 阶 + 1 阶
> ```  

## 动态规划

#### 思路

观察结果：

`n=1` 时，`1` 种；
`n=2` 时，`2` 种；
`n=3` 时，`3` 种；
`n=4` 时，`5` 种；
...

不难发现，第 `n` 次的结果是 `n-1` 次与 `n-2` 次之和。

当然你写递归也行，但是那样总是有很多重复计算，不如来动态规划，存储之前算过的：

```csharp
class Solution
{
    public int ClimbStairs(int n)
    {
        var table = new int[n - 1]; // 创建表

        if (n <= 3) return n;       // i <= 3时，返回相应结果即可

        // 初始化表
        table[1] = 2;
        table[2] = 3;

        // 计算前两次的值并存储
        for (int i = 3; i < n - 1; i++)
        {
            table[i] = table[i - 1] + table[i - 2];
        }

        return table[n - 2] + table[n - 3];
    }
}
```

## 排列组合脑瘫算法

#### 思路

来举例子吧：

`n=1` 时，`1` 种：

```
1
```

`n=2` 时，`1+1` 种：

```
2
1 1
```

`n=3` 时，`2+1` 种：

```
2 1     // C(2,1)
1 1 1
```

`n=4` 时，`1+3+1` 种：

```
2 2
2 1 1   // C(3,1)
1 1 1 1
```

`n=5` 时，`` 种：

```
2 2 1   // C(3,2)
2 1 1 1 // C(4,1)
1 1 1 1 1
```

好，找到规律了：

1. 判断奇数还是偶数，是奇数 result + 1，是偶数 result + 2；
2. y = (x - 1) / 2；
3. i 循环 y 次，result + C(x - i, i)， i++

超时，然后优化计算组合数部分，使用 `HashSet<T>` 来存储分子然后相消。

总之想这个挺有趣的呢。

最终代码：
```csharp
    public class Solution
    {
        public static void Main()
        {
            var t = new Solution();
            Console.WriteLine(t.ClimbStairs(44));
        }

        public int ClimbStairs(int n)
        {
            var result = IsEven(n) ? 2 : 1;  // 奇数偶数预先加值
            var cnt = (n - 1) >> 1;         // 循环组合次数

            for (var i = 1; i <= cnt; i++)
                result += Combine(i, n - i);    // 计算组合值

            return result;
        }

        /// <summary>
        /// 是否为奇数
        /// </summary>
        /// <param name="num">数字</param>
        /// <returns>是否为奇数</returns>
        private static bool IsEven(int num)
            => (num & 1) == 0;

        /// <summary>
        /// 是否为奇数
        /// </summary>
        /// <param name="num">数字</param>
        /// <returns>是否为奇数</returns>
        private static bool IsEven(long num)
            => (num & 1) == 0;

        /// <summary>
        /// 计算阶乘
        /// </summary>
        /// <param name="x">阶数</param>
        /// <returns>x!</returns>
        private long Factorial(long x)
                => x <= 1 ? 1 : x * Factorial(x - 1);

        /// <summary>
        /// 组合
        /// </summary>
        /// <param name="m">取出元素的个数</param>
        /// <param name="n">元素总个数</param>
        /// <returns></returns>
        private int Combine(int m, int n)
        {
            // 分子值
            long moleculeValue = 1;
            long denominatorValue;

            // 分子的集合
            var moleculesList = new HashSet<int>();
            for (var i = 1; i <= n; i++)
            {
                moleculesList.Add(i);
            }

            // 分母的集合
            var denominatorsList = new LinkedList<int>();

            // 选取较少的一项进行计算
            int denominatorCnt;

            var o = n - m;
            // 分母值
            if (o > m)
            {
                denominatorValue = Factorial(m);
                denominatorCnt = o;
            }
            else
            {
                denominatorValue = Factorial(o);
                denominatorCnt = m;
            }

            // 进行分子分母相消
            for (var i = 1; i <= denominatorCnt; i++)
            {
                if (moleculesList.Contains(i))
                {
                    moleculesList.Remove(i);
                }
                else
                {
                    denominatorsList.AddLast(i);
                }
            }

            // 计算分母值
            foreach (var val in denominatorsList)
            {
                denominatorValue *= val;
            }
            // 计算分子值
            foreach (var val in moleculesList)
            {
                if (IsEven(val) && IsEven(denominatorValue))
                {
                    denominatorValue >>= 1;
                    moleculeValue *= (val >> 1);
                }
                else
                {
                    moleculeValue *= val;
                }

            }

            return (int)(moleculeValue / denominatorValue);
        }
    }
```