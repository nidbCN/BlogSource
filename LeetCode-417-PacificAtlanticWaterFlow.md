
## 题干

> 给定一个 ` m x n ` 的非负整数矩阵来表示一片大陆上各个单元格的高度。“太平洋”处于大陆的左边界和上边界，而“大西洋”处于大陆的右边界和下边界。
> 
> 规定水流只能按照上、下、左、右四个方向流动，且只能从高到低或者在同等高度上流动。
> 
> 请找出那些水流既可以流动到“太平洋”，又能流动到“大西洋”的陆地单元的坐标。
> 
> **提示：**
> 
> 输出坐标的顺序不重要
> 
> m 和 n 都小于150
> 
> **示例：**
> 
> ```
> 给定下面的 5x5 矩阵:
> 
>   太平洋 ~   ~   ~   ~   ~ 
>        ~  1   2   2   3  (5) *
>        ~  3   2   3  (4) (4) *
>        ~  2   4  (5)  3   1  *
>        ~ (6) (7)  1   4   5  *
>        ~ (5)  1   1   2   4  *
>           *   *   *   *   * 大西洋
> 
> 返回:
> 
> [[0, 4], [1, 3], [1, 4], [2, 2], [3, 0], [3, 1], [4, 0]] (上图中带括号的单元).
> ```


## 深度优先搜索

#### 思路

找出太平洋和大西洋都能抵达的点即可。

#### 代码

```csharp
public IList<IList<int>> PacificAtlantic(int[][] heights)
{
    var res = new List<IList<int>>();
    var m = heights.Length;
    var n = heights[0].Length;

    // 记录坐标地是否到达太平洋、大西洋
    var canReachP = new bool[m, n];
    var canReachX = new bool[m, n];
    
    // 上下左右出发，深度优先搜索
    for (int i = 0; i < m; i++)
    {
        DepthFirstSearch(heights, canReachP, i, 0);
        DepthFirstSearch(heights, canReachX, i, n - 1);
    }
    
    for (int j = 0; j < n; j++)
    {
        DepthFirstSearch(heights, canReachP, 0, j);
        DepthFirstSearch(heights, canReachX, m - 1, j);
    }
    // 遍历记录，如果都可到达即可加入结果
    for (int i = 0; i < m; i++)
    {
        for (int j = 0; j < n; j++)
        {
            if (canReachP[i, j] && canReachX[i, j])
            {
                res.Add(new int[] { i, j });
            }
        }
    }
    return res;
}

public void DepthFirstSearch(int[][] heights, bool[,] canReach, int i, int j)
{
    // 如果已经扫描过可达就不用扫描
    if (canReach[i, j]) return;
    // 扫描过即说明可达，这也是逆流的优点
    canReach[i, j] = true;
    // 上下左右深度搜索，越界就不搜索
    if (i - 1 >= 0 && heights[i - 1][j] >= heights[i][j])
    {
        DepthFirstSearch(heights, canReach, i - 1, j);
    }

    if (j - 1 >= 0 && heights[i][j - 1] >= heights[i][j])
    {
        DepthFirstSearch(heights, canReach, i, j - 1);
    }

    if (i + 1 < heights.Length && heights[i + 1][j] >= heights[i][j])
    {
        DepthFirstSearch(heights, canReach, i + 1, j);
    }

    if (j + 1 < heights[0].Length && heights[i][j + 1] >= heights[i][j])
    {
        DepthFirstSearch(heights, canReach, i, j + 1);
    }
}
```