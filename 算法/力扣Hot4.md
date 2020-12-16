# 55 跳跃游戏

给定一个非负整数数组，你最初位于数组的第一个位置。

数组中的每个元素代表你在该位置可以跳跃的最大长度。

判断你是否能够到达最后一个位置。

**示例** :

```
输入: [2,3,1,1,4]
输出: true
解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。
```

**题解**

``` javascript
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var canJump = function(nums) {
    const n = nums.length;
    let j = 0;
    // 类似于动态规划的思想
    for(let i=0; i<n; i++) {
        // 当前索引大于能到的最远 返回false
        if(i>j) return false;
        // 找当前能到达的最远的下标
        j = Math.max(j, i+nums[i]);
    }
    return true;
};
```



# 56 合并区间

给出一个区间的集合，请合并所有重叠的区间。

**示例:**

```
输入: intervals = [[1,3],[2,6],[8,10],[15,18]]
输出: [[1,6],[8,10],[15,18]]
解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
```

**题解：**

``` javascript
/**
 * @param {number[][]} intervals
 * @return {number[][]}
 */
var merge = function(intervals) {
    if(intervals.length === 0) return [];
    intervals.sort((a, b) => a[0] - b[0]);
        // 确保数组集合整体有序（升序）
    // 因为子项内部都是有序的 故只需比较各自首项即可实现排序
    intervals.sort((cur, next) => cur[0] - next[0])

    // 变量定义与遍历
    let i = 0
    while (i < intervals.length - 1) {
        const curRight = intervals[i][1]
        const [nextLeft, nextRight] = intervals[i + 1]

        if (curRight >= nextLeft) {
            // 将后一项数组与当前项数组合并 -> 合并到当前项上
            intervals[i][1] = Math.max(curRight, nextRight)
            // 合并之后 原地移除后项
            intervals.splice(i + 1, 1)
        }else {
            i++
        }
    }
    // 返回结果 （时间复杂度 O（N）空间复杂度 O（1））
    return intervals
};
```



# 62 不同路径

一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。

机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

问总共有多少条不同的路径？

**示例 **：

```输入：m = 3, n = 7
输入：m = 3, n = 7
输出：28
```

**题解：**

``` javascript
/**
 * @param {number} m
 * @param {number} n
 * @return {number}
 */
var uniquePaths = function(m, n) {
    const map = [];
    for(let i=0;i<n;i++) {
        map.push([]);
        for(let j=0;j<m;j++) {
            map[i].push(0);
        }
    }
    // 动态规划 
    //当前格的最多路径=左边的格的最大路径+上方的格的最大路径
    map[0].fill(1);
    for(let i=1;i<n;i++) map[i][0] = 1;
    for(let i=1;i<n;i++) {
        for(let j=1;j<m;j++) {
            map[i][j] = map[i-1][j] + map[i][j-1];
        }
    }
    return map[n-1][m-1];
};
```



# 64 最小路径和

给定一个包含非负整数的 m x n 网格 grid ，请找出一条从左上角到右下角的路径，使得路径上的数字总和为最小。

说明：每次只能向下或者向右移动一步。

 

**示例 **：

```
输入：grid = [[1,3,1],[1,5,1],[4,2,1]]
输出：7
解释：因为路径 1→3→1→1→1 的总和最小。
```

**实现：**

``` javascript
/**
 * @param {number[][]} grid
 * @return {number}
 */
var minPathSum = function(grid) {
    const rows = grid.length;
    const cols = grid[0].length;
    // 初始化dp
    const dp = [];
    for(let i=0;i<rows;i++) {
        dp.push([]);
        for(let j=0;j<cols;j++) {
            dp[i].push(0);
        }
    }
    // 初始化第一行第一列
    dp[0][0] = grid[0][0];
    for(let i=1;i<cols;i++) dp[0][i] = dp[0][i-1] + grid[0][i];
    for(let i=1;i<rows;i++) dp[i][0] = dp[i-1][0] + grid[i][0];
    // 双层for循环更新dp数组
    for(let i=1;i<rows;i++) {
        for(let j=1;j<cols;j++) {
            const l1 = grid[i][j] + dp[i][j-1];
            const l2 = grid[i][j] + dp[i-1][j];
            dp[i][j] = l1 > l2 ? l2 : l1;
        }
    }
    return dp[rows-1][cols-1];
};
```



# 70 爬楼梯

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

**示例 **：

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。

1.  1 阶 + 1 阶
2.  2 阶
```

**实现：**

``` javascript
/**
 * @param {number} n
 * @return {number}
 */
var climbStairs = function(n) {
    if(n === 1) return 1;
    if(n === 2) return 2;
    let step1=1, step2=2;
    // 动态规划思想
    for(let i=3;i<=n;i++) {
        [step1, step2] = [step2, step1];
        step2 = step1 + step2;
    }
    return step2;
};
```



# 75 颜色分类

给定一个包含红色、白色和蓝色，一共 n 个元素的数组，原地对它们进行排序，使得相同颜色的元素相邻，并按照红色、白色、蓝色顺序排列。

此题中，我们使用整数 0、 1 和 2 分别表示红色、白色和蓝色。

进阶：

你可以不使用代码库中的排序函数来解决这道题吗？
你能想出一个仅使用常数空间的一趟扫描算法吗？

**示例** ：

``` javascript
输入：nums = [2,0,2,1,1,0]
输出：[0,0,1,1,2,2]
```



**实现：**

``` javascript
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var sortColors = function(nums) {
    const n = nums.length;
    if(n<2) return;

    const swap = (nums, idx1, idx2) => {
        const tmp = nums[idx1];
        nums[idx1] = nums[idx2];
        nums[idx2] = tmp;
    }

    //  0: [0, zero]
    //  1: [zero, i]
    //  2: [i, two]
    let i = 0;
    let zero = -1;
    let two = n -1;

    while(i <= two) {
        if(nums[i] === 0) {
            zero++;
            swap(nums, i, zero);
            i++;
        } else if(nums[i] === 1) {
            i++;
        } else {
            swap(nums, i, two);
            two--;
        }
    }
    return nums;
};
```



# 78 子集

给定一组不含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。

说明：解集不能包含重复的子集。

**示例**:

```
输入: nums = [1,2,3]
输出:

[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
var subsets = function(nums) {
    const ret = [];
    ret.push([]);
    // DFS深度优先遍历
    const travel = (idx,arr) => {
        for(let i=idx;i<nums.length;i++) {
            const temp = [...arr,nums[i]];
            ret.push(temp);
            // 数组在长度范围内 递归
            if(temp.length < nums.length) travel(i+1,temp);
        }
    }
    travel(0,[])
    return ret;
};
```



# 94  二叉树的中序遍历

给定一个二叉树的根节点 `root` ，返回它的 **中序** 遍历。

 

**示例 ：**

![img](https://assets.leetcode.com/uploads/2020/09/15/inorder_1.jpg)

```
输入：root = [1,null,2,3]
输出：[1,3,2]
```

**实现：**

``` javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var inorderTraversal = function(root) {
    const nodes = [];
    const result = [];
    if(!root) return [];
    nodes.push(root);
    while(nodes.length !== 0) {
        const cur = nodes.shift();
        // 设置已访问 当已访问过证明该节点的子节点已经被添加到数组中
        // 再次访问只需要得到值
        if(!cur.visted) {
            cur.visted = true;
            if(cur.right && !cur.right.visted) nodes.unshift(cur.right);
            nodes.unshift(cur);
            if(cur.left && !cur.left.visted) nodes.unshift(cur.left);
        } 
        else {
            result.push(cur.val);
        }
    }
    return result;
};
```



# 96 不同的二叉树搜索树

给定一个整数 n，求以 1 ... n 为节点组成的二叉搜索树有多少种？

**示例**:

```
输入: 3
输出: 5
解释:
给定 n = 3, 一共有 5 种不同结构的二叉搜索树:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```



**题解：**

``` javascript
/**
 * @param {number} n
 * @return {number}
 */
var numTrees = function(n) {
    let cur = 1;
    for(let i=0; i<n;i++) {
        cur = cur * (4*i+2) / (i+2);
    }
    return cur;
};
```



# 98 验证二叉搜索树

给定一个二叉树，判断其是否是一个有效的二叉搜索树。

假设一个二叉搜索树具有如下特征：

节点的左子树只包含小于当前节点的数。
节点的右子树只包含大于当前节点的数。
所有左子树和右子树自身必须也是二叉搜索树。
**示例** :

```
输入:
    2
   / \
  1   3
输出: true
```



**题解**：

``` javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {boolean}
 */

var isValidBST = function(root) {
    const stack = [];
    // 递归遍历 
    // 获得一个中序遍历的数组
    const dfs = (root) => {
        if(!root)   return;
        if(root.left)   dfs(root.left);
        stack.push(root.val);
        if(root.right)  dfs(root.right);
    }
    dfs(root);
    // 判断是否递增的
    for(let i=0;i<stack.length-1;i++) {
        if(stack[i] >= stack[i+1]) return false;
    }
    return true;
};
```



