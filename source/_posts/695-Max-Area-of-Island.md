---
title: '695. Max Area of Island - LeetCode'
date: 2022-06-18 00:18:45
categories:
  - [Code,C#]
  - [Topic,LeetCode]
---
In this part, I try to solve the problem of 659 with C#, and this level is medium.

# Problem
<div class="content__u3I1 question-content__JfgR"><div><p>You are given an <code>m x n</code> binary matrix <code>grid</code>. An island is a group of <code>1</code>'s (representing land) connected <strong>4-directionally</strong> (horizontal or vertical.) You may assume all four edges of the grid are surrounded by water.</p>

<p>The <strong>area</strong> of an island is the number of cells with a value <code>1</code> in the island.</p>

<p>Return <em>the maximum <strong>area</strong> of an island in </em><code>grid</code>. If there is no island, return <code>0</code>.</p>
<p><strong>Example 1:</strong></p>
{% asset_img "maxarea1-grid.jpg" %}
<pre><strong>Input:</strong> grid = [[0,0,1,0,0,0,0,1,0,0,0,0,0],[0,0,0,0,0,0,0,1,1,1,0,0,0],[0,1,1,0,1,0,0,0,0,0,0,0,0],[0,1,0,0,1,1,0,0,1,0,1,0,0],[0,1,0,0,1,1,0,0,1,1,1,0,0],[0,0,0,0,0,0,0,0,0,0,1,0,0],[0,0,0,0,0,0,0,1,1,1,0,0,0],[0,0,0,0,0,0,0,1,1,0,0,0,0]]
<strong>Output:</strong> 6
<strong>Explanation:</strong> The answer is not 11, because the island must be connected 4-directionally.
</pre>

<p><strong>Example 2:</strong></p>

<pre><strong>Input:</strong> grid = [[0,0,0,0,0,0,0,0]]
<strong>Output:</strong> 0
</pre>

<p>&nbsp;</p>
<p><strong>Constraints:</strong></p>

<ul>
	<li><code>m == grid.length</code></li>
	<li><code>n == grid[i].length</code></li>
	<li><code>1 &lt;= m, n &lt;= 50</code></li>
	<li><code>grid[i][j]</code> is either <code>0</code> or <code>1</code>.</li>
</ul>
</div></div>

# My Answer

I use the DFS to solve this problem.

Concept following:
1. Loop grid
2. Traverse all island and compare the max count of island areas
3. Get the max result of island

I split the program to partial class to help us easy to read, and define an enum of area to mark the traversal.
Finally, I use the function TraverseIsland that is an algorithm of DFS to help us traverse the four directions. 

```csharp
public partial class Solution {      
    
    /// <summary>
    /// MaxAreaOfIsland
    /// </summary>
    /// <param name="grid">grid</param>
    /// <returns>maximum area of an island</returns>
    public int MaxAreaOfIsland(int[][] grid) {
        var maxAreaOfIsland = 0;
        for(var i=0;i<grid.Length;i++)
        {
            for(var j=0;j<grid[0].Length;j++)
            {
                if(IsIsland(grid[i][j]))
                {
                    var islandAreas = new List<(int,int)>();
                    TraverseIsland(islandAreas,grid,i,j);
                    maxAreaOfIsland = Math.Max(islandAreas.Count,maxAreaOfIsland);
                }
            }
        }
        return maxAreaOfIsland;
    }
}

///
/// private method & variant
///
public partial class Solution {      
    
    private enum AreaEnum 
    {
      None = 0,
      Island = 1,
      HasBeenTraverse = 2,
    };

    /// <summary>
    /// Check the location that is island
    /// </summary>
    /// <param name="gridValue">value of grid</param>
    /// <returns>bool</returns>
    private bool IsIsland(int gridValue)
    {
        return gridValue == (int)AreaEnum.Island;
    }
    
    /// <summary>
    /// Mark the traversal to avoid infinite loops
    /// </summary>
    /// <param name="grid">grid</param>
    /// <param name="i">i of grid</param>
    /// <param name="j">j of grid</param>
    private void MarkTraversal(int[][] grid,int i,int j)
        => grid[i][j] = (int)AreaEnum.HasBeenTraverse;
    
    /// <summary>
    /// Traverse Island
    /// Traverse the grid 4-directionally
    /// </summary>
    /// <param name="islandAreas">areas of the island</param>
    /// <param name="grid">grid object</param>
    /// <param name="i">i of grid</param>
    /// <param name="j">j of grid</param>
    private void TraverseIsland (
        List<(int,int)> islandAreas, 
        int[][] grid,
        int i, 
        int j)
    {
        islandAreas.Add((i,j));
        MarkTraversal(grid,i,j);
        
        // top
        if(i-1 >= 0 && IsIsland(grid[i-1][j])) 
            TraverseIsland(islandAreas,grid,i-1,j);
        // bottom
        if(i+1 < grid.Length && IsIsland(grid[i+1][j]))
            TraverseIsland(islandAreas,grid,i+1,j);
        // left
        if(j-1 >= 0 && IsIsland(grid[i][j-1])) 
            TraverseIsland(islandAreas,grid,i,j-1);
        // right
        if(j+1 < grid[0].Length && IsIsland(grid[i][j+1]))
            TraverseIsland(islandAreas,grid,i,j+1);
    }
}
```

Run Result:
```
Runtime: 98 ms, faster than 93.42% of C# online submissions for Max Area of Island.
Memory Usage: 43.9 MB, less than 6.70% of C# online submissions for Max Area of Island.
```