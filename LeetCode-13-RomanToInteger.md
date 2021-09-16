title: '罗马数字转整数-力扣13 遍历吧'
author: Gaein nidb
categories:
  - 代码如诗
tags:
  - 算法
  - 笔记
  - CSharp
date: 2021-09-16 13:52:23
---

> 把罗马数字转成 `int` ，遍历字符串进行计算即可。

## 题干

> 罗马数字包含以下七种字符: `I`，`V`，`X`，`L`，`C`，`D` 和 `M`。
> 
> ```
> 字符          数值
> I             1
> V             5
> X             10
> L             50
> C             100
> D             500
> M             1000
> ```
> 
> 例如， 罗马数字 2 写做 II ，即为两个并列的 1。12 写做 XII ，即为 X + II 。 27 写做  XXVII, 即为 XX + V + II 。
> 
> 通常情况下，罗马数字中小的数字在大的数字的右边。但也存在特例，例如 4 不写做 IIII，而是 IV。数字 1 在数字 5 的左边，所表示的数等于大数 5 减小数 1 得到的数值 4 。同样地，数字 9 表示为 IX。这个特殊的规则只适用于以下六种情况：
> 
> * I 可以放在 V (5) 和 X (10) 的左边，来表示 4 和 9。
> * X 可以放在 L (50) 和 C (100) 的左边，来表示 40 和 90。 
> * C 可以放在 D (500) 和 M (1000) 的左边，来表示 400 和 900。
> 
> 给定一个罗马数字，将其转换成整数。输入确保在 1 到 3999 的范围内。
> 
> **示例 1:** 
> ```
> 输入: "III"
> 输出: 3
> ```
> 
> **示例 2:** 
> ```
> 输入: "IV"
> 输出: 4
> ```
> 
> **示例 3:** 
> ```
> 输入: "IX"
> 输出: 9
> ```
> 
> **示例 4:** 
> ```
> 输入: "LVIII"
> 输出: 58
> 解释: L = 50, V= 5, III = 3.
> ```
> 
> **示例 5:** 
> ```
> 输入: "MCMXCIV"
> 输出: 1994
> 解释: M = 1000, CM = 900, XC = 90, IV = 4.
> ```
> 
> **提示：** 
> 
> * `1 <= s.length <= 15`
> * `s` 仅含字符 `('I', 'V', 'X', 'L', 'C', 'D', 'M')`
> * 题目数据保证 `s` 是一个有效的罗马数字，且表示整数在范围 `[1, 3999]` 内
> * 题目所给测试用例皆符合罗马数字书写规则，不会出现跨位等情况。
> * IL 和 IM 这样的例子并不符合题目要求，49 应该写作 XLIX，999 应该写作 CMXCIX 。
> * 关于罗马数字的详尽书写规则，可以参考 [罗马数字 - Mathematics](https://b2b.partcommunity.com/community/knowledge/zh_CN/detail/10753/) 。  
> 

## 自己想的慢速解法

#### 思路

罗马数字每个数字由一到两个字符构成：

```json
[
    { "I":  1    },
    { "V":  5    },
    { "X":  10   },
    { "L":  50   },
    { "C":  100  },
    { "D":  500  },
    { "M":  1000 },
    { "IV": 4    },
    { "IX": 9    },
    { "XL": 40   },
    { "XC": 90   },
    { "CD": 400  },
    { "CM": 900  }
]
```

首先遍历读取字符串的每个字符，遇到 `I`, `X`, `C` 即为有可能和下一个字符组成一个罗马数字。

维护一个字典，存储所有的单个罗马数字 ( `IX`, `C` 这种) ，对字符串进行遍历，遇到可能有下一个字母组成罗马数字的就检查下一个是否越界，如果没有越界就查询字典，字典有就加上相应的数值并且 `i += 2` ，没有就查询当前字符，获取数值并加到 `result` 里，然后 `i++`。

#### 代码

```csharp
public class Solution
{
    private static bool MayHasNext(char ch)
        => (ch) switch
        {
            'I' => true,
            'X' => true,
            'C' => true,
            _ => false,
        };

    public static bool TryParse(char ch, out int num)
        => TryParse(ch.ToString(), out num);

    public static bool TryParse(char ch1, char ch2, out int num)
        => TryParse($"{ch1}{ch2}", out num);

    public static bool TryParse(string s, out int num)
        => (num = s switch
        {
            "I" => 1,
            "V" => 5,
            "X" => 10,
            "L" => 50,
            "C" => 100,
            "D" => 500,
            "M" => 1000,
            "IV" => 4,
            "IX" => 9,
            "XL" => 40,
            "XC" => 90,
            "CD" => 400,
            "CM" => 900,
            _ => -1,
        }) != -1;


    public int RomanToInt(string s)
    {
        if (s is null) throw new ArgumentNullException(nameof(s));
        if (s == string.Empty) return 0;

        var result = 0;

        var i = 0;
        while (i < s.Length)
        {
            var ch = s[i];
            if (MayHasNext(ch) && i < s.Length - 1)
            {
                if (TryParse(ch, s[i + 1], out var numOfTwo))
                {
                    result += numOfTwo;
                    i += 2;
                    continue;
                }
            }

            if (TryParse(ch, out var num))
                result += num;

            i++;
        }

        return result;
    }
}
```

## 大佬的高速解法

#### 思路

罗马数字的规则，用一句话来说就是：“把一个小值放在大值的左边，就是做减法，否则为加法”。也就是我们可以维护一个变量来存储上一个数字，然后比大小进行加减法。

#### 代码

```csharp
public int RomanToInt(string s)
{
    var strSpan = s.AsSpan();

    var result = 0;
    var lastNum = GetRomanCharValue(strSpan[0]);

    foreach(var ch in  strSpan[1..])
    {
        var num = GetRomanCharValue(ch);

        if (lastNum < num)
        {
            result -= lastNum;
        }
        else
        {
            result += lastNum;
        }

        lastNum = num;
    }

    return result + lastNum;
}

private static int GetRomanCharValue(char ch)
    => ch switch
    {
        'I' => 1,
        'V' => 5,
        'X' => 10,
        'L' => 50,
        'C' => 100,
        'D' => 500,
        'M' => 1000,
        _ => throw new ArgumentException($"Unknow roman char {ch}"),
    };
```