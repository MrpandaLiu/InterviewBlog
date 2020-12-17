# 287 寻找重复数

给定一个包含 n + 1 个整数的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。

**示例**:

```
输入: [1,3,4,2,2]
输出: 2
```

**题解：**

``` javascript

/**
 * @param {number[]} nums
 * @return {number}
 */
var findDuplicate = function(nums) {
    // 数组索引值范围0到n, 元素数值范围1到n 小于索引范围, 故可以用数组元素的值当作链表指针
    // 思路参考寻找有环链表环起始点
	// 如果有环，快指针一定会和慢指针在环上相遇
	// 找到相遇点，这时令一个指针从链表头部起始，速度和慢指针相同，他们的相遇点即为所求
    let L = R = 0, begin;
    while (1) {
        L = nums[L];
        R = nums[nums[R]];
        if (L === R) {
            begin = 0;
            while (begin !== L) {
                begin = nums[begin];
                L = nums[L];
            }
            return begin;
        }
    }
};
```



# 300 最长递增子序列

给你一个整数数组 nums ，找到其中最长严格递增子序列的长度。

子序列是由数组派生而来的序列，删除（或不删除）数组中的元素而不改变其余元素的顺序。例如，[3,6,2,7] 是数组 [0,3,1,6,2,2,7] 的子序列。

**示例**：

```
输入：nums = [10,9,2,5,3,7,101,18]
输出：4
解释：最长递增子序列是 [2,3,7,101]，因此长度为 4 。
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var lengthOfLIS = function(nums) {
    if(nums.length === 0) return 0;
    let max = 1;
    const dp = new Array(nums.length).fill(1);
    // 当前数 大于 前面某一个数的话
    // 那么 当前数dp = 前面某数的dp值+1 和 当前数dp的最大值
    for(let i=0; i<nums.length; i++) {
        for(let j=0; j<i; j++) {
            if(nums[j]<nums[i]) {
                dp[i] = Math.max(dp[i], dp[j]+1);
            }
        }
        max = Math.max(max, dp[i]);
    }
    return max;
};
```



# 309 最佳买卖股票时机含冷冻期

暂时跳过



# 322 零钱交换

给定不同面额的硬币 coins 和一个总金额 amount。编写一个函数来计算可以凑成总金额所需的最少的硬币个数。如果没有任何一种硬币组合能组成总金额，返回 -1。

你可以认为每种硬币的数量是无限的。

**示例 ：**

```
输入：coins = [1, 2, 5], amount = 11
输出：3 
解释：11 = 5 + 5 + 1
```

**题解：**

``` javascript
/**
 * @param {number[]} coins
 * @param {number} amount
 * @return {number}
 */
var coinChange = function(coins, amount) {
    if(amount === 0) return 0;
    // 初始化dp数组
    const dp = new Array(amount+1).fill(Infinity);
    dp[0] = 0;
    for(let coin of coins) {
        for(let i=coin; i<=amount; i++) {
            // 通过状态转移函数 更新dp = dp or dp + cur
            dp[i] = Math.min(dp[i], dp[i-coin]+1);
        }
    }
    // 如果没找到返回-1
    return dp[amount] === Infinity ? -1 : dp[amount];
};
```



# 337 打家劫舍III

在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

**示例 **:

    输入: [3,2,3,null,3,null,1]
    	 3
        / \
       2   3
        \   \ 
         3   1
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
 * @return {number}
 */
var rob = function(root) {
    const f = new Map();
    const g = new Map();
    const dfs = (node) => {
        if(node === null) return;
        // 后序遍历
        // 由底层至上逐层获取获取最佳
        dfs(node.left);
        dfs(node.right);
        // 设置选or不选的最佳情况
        f.set(node, node.val + (g.get(node.left) || 0) + (g.get(node.right) || 0));
        g.set(node, Math.max(f.get(node.left) || 0, g.get(node.left) || 0) + Math.max(f.get(node.right) || 0, g.get(node.right) || 0));
    }
    dfs(root);
    return Math.max(f.get(root) || 0, g.get(root) || 0);
};
```

