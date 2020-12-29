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



# 338 比特位计数


给定一个非负整数 **num**。对于 **0 ≤ i ≤ num** 范围中的每个数字 **i** ，计算其二进制数中的 1 的数目并将它们作为数组返回。

**示例 1:**

```
输入: 2
输出: [0,1,1]
```

**示例 2:**

```
输入: 5
输出: [0,1,1,2,1,2]
```

**题解：**

``` javascript
/**
 * @param {number} num
 * @return {number[]}
 */
var countBits = function(num) {
    const dp = [];
    dp[0] = 0;
    // 如果是奇数比前一个数多1
    // 如果是偶数可以理解为它一半的那个数 左移1位
    for(let i=0; i<=num; i++) {
        if(i%2 === 0) dp[i]  = dp[i/2];
        else dp[i] = dp[i-1] + 1;
    }
    return dp;
};
```



# 347 前K个高频元素


给定一个非空的整数数组，返回其中出现频率前 ***k\*** 高的元素。



**示例 :**

```
输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number[]}
 */
var topKFrequent = function(nums, k) {
    const map = new Map();
    const array = [];
    for(let item of nums) {
        if(map.has(item)) map.set(item, map.get(item)+1);
        else map.set(item, 1);
    }
    for(let [key, value] of map) {
        array.push({
            key,
            value
        })
    }
    arr.sort((a,b) => a.value - b.value);
    return array.slice(0, k).map(item => {
        return item.key;
    })
}
```



# 394 字符串解码

给定一个经过编码的字符串，返回它解码后的字符串。

编码规则为: k[encoded_string]，表示其中方括号内部的 encoded_string 正好重复 k 次。注意 k 保证为正整数。

你可以认为输入字符串总是有效的；输入字符串中没有额外的空格，且输入的方括号总是符合格式要求的。

此外，你可以认为原始数据不包含数字，所有的数字只表示重复的次数 k ，例如不会出现像 3a 或 2[4] 的输入。

 

**示例**：

```
输入：s = "3[a]2[bc]"
输出："aaabcbc"
```



**题解：**

``` javascript
/**
 * @param {string} s
 * @return {string}
 */
var decodeString = (s) => {
    const stack = [];
    for(let char of s) {
        // ]前的字符都入栈
        if(char !== ']') {
            stack.push(char);
            continue;
        }
        // 弹出一个来检测
        let cur = stack.pop();
   		let str = '';
        // 接下来遇到字符 直到遇到[
        while(cur !== '[') {
            str = cur + str;
            cur = stack.pop();
        }
        // 此时cur为[  接下来要遇到数字
        let num = '';
        cur = stack.pop();
        while(!isNaN(cur)) {
            num = cur + num;
            cur = stack.pop();
        }
        // 现在要么是字母 要么是[
        stack.push(cur);
        stack.push(str,repeat(num));
    }
    return stack.join('');
}
```



# 399 除法求值

给出方程式 A / B = k, 其中 A 和 B 均为用字符串表示的变量， k 是一个浮点型数字。根据已知方程式求解问题，并返回计算结果。如果结果不存在，则返回 -1.0。

输入总是有效的。你可以假设除法运算中不会出现除数为 0 的情况，且不存在任何矛盾的结果。

 

**示例**：

```
输入：equations = [["a","b"],["b","c"]], values = [2.0,3.0], queries = [["a","c"],["b","a"],["a","e"],["a","a"],["x","x"]]
输出：[6.00000,0.50000,-1.00000,1.00000,-1.00000]
解释：
给定：a / b = 2.0, b / c = 3.0
问题：a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ?
返回：[6.0, 0.5, -1.0, 1.0, -1.0 ]
```



**题解：**

``` javascript
/**
 * @param {string[][]} equations
 * @param {number[]} values
 * @param {string[][]} queries
 * @return {number[]}
 */
var calcEquation = function(equations, values, queries) {
  let ans = [],
      elen = equations.length,
      vlen = values.length,
      qlen = queries.length,
      matrix = [], // 矩阵
      map = {}; // 判断 queries 中的点是否存在于已知的图中
  
  // 创建有向图
  for (let i = 0; i < elen; i++) {
    let [start, end] = equations[i],
        val = values[i];
    
    if (matrix[start] === undefined) matrix[start] = [];
    if (matrix[end] === undefined) matrix[end] = [];
    
    matrix[start][end] = val;
    matrix[end][start] = 1 / val;
    
    if (map[ start ] === undefined) map[ start ] = 1;
    if (map[ end ] === undefined) map[ end ] = 1;
  }
  
  for (let i = 0; i < qlen; i++) {
    let [start, end] = queries[i];
    if (map[start] === undefined || map[end] === undefined) {
      ans.push( -1.0 );
      continue;
    }
    ans.push( dfs(start, end, []) );
  }
  
  // 搜索 A 到 B 的路径
  function dfs(A, B, visited) {
    if (A === B) return 1.0;
    
    visited[A] = 1
    
    let neighbors = matrix[A];
    
    let target = null;
    
    for (let key in neighbors) {
      let val = neighbors[key];
      if (visited[key] !== undefined) {
        continue;
      }
      
      let d = dfs(key, B, visited);
      
      if (d > 0) {
        target = d * matrix[A][key];
        break;
      }
    }
    
    return target || -1.0;
  }
  
  return ans;
};
```



# 406 根据身高重建队列

假设有打乱顺序的一群人站成一个队列，数组 people 表示队列中一些人的属性（不一定按顺序）。每个 people[i] = [hi, ki] 表示第 i 个人的身高为 hi ，前面 正好 有 ki 个身高大于或等于 hi 的人。

请你重新构造并返回输入数组 people 所表示的队列。返回的队列应该格式化为数组 queue ，其中 queue[j] = [hj, kj] 是队列中第 j 个人的属性（queue[0] 是排在队列前面的人）。

 

**示例**：

```
输入：people = [[7,0],[4,4],[7,1],[5,0],[6,1],[5,2]]
输出：[[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]]
解释：
编号为 0 的人身高为 5 ，没有身高更高或者相同的人排在他前面。
编号为 1 的人身高为 7 ，没有身高更高或者相同的人排在他前面。
编号为 2 的人身高为 5 ，有 2 个身高更高或者相同的人排在他前面，即编号为 0 和 1 的人。
编号为 3 的人身高为 6 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
编号为 4 的人身高为 4 ，有 4 个身高更高或者相同的人排在他前面，即编号为 0、1、2、3 的人。
编号为 5 的人身高为 7 ，有 1 个身高更高或者相同的人排在他前面，即编号为 1 的人。
因此 [[5,0],[7,0],[5,2],[6,1],[4,4],[7,1]] 是重新构造后的队列。
```



**题解：**

``` javascript
/**
 * @param {number[][]} people
 * @return {number[][]}
 */
var reconstructQueue = function (people) {
    // 将people按身高从大到小排序，如果身高一样则将前面高于自己人数小的人放在前面
    people.sort((a, b) => a[0] === b[0] ? a[1] - b[1] : b[0] - a[0])
    // 创建新数组 ans
    let ans = []
    for (let i = 0; i < people.length; i++) {
        // 挨个根据前面高于自己人数插入到ans里
        // 因为people已按照身高排序，所以某个人被插入到ans里时，所有比他高的都已经在ans里了
        // 而身高比他矮的人怎样插入到ans里都不影响前面高于他的人数
        // 所以这样得到的数组就是符合我们要求的队列
        ans.splice(people[i][1],0,people[i])
    }
    return ans
};
```



# 416 分割等和子集

给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。

**注意**:

```
每个数组中的元素不会超过 100
数组的大小不会超过 200
示例 1:

输入: [1, 5, 11, 5]

输出: true

解释: 数组可以分割成 [1, 5, 5] 和 [11].
```



**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {boolean}
 */
var canPartition = function(nums) {
    const target = nums.reduce((a, b) => a + b) / 2;
    // 和为奇数 -》 false
    if(target % 1 !== 0) return false;
    const dp = new Array(target+1).fill(false);
    dp[0] = true;
    // 要找一个子数组 可以达到target
    // 如果能到达i 则能达到target-i即可
    for(const n of nums) {
        for(let i=target; i>=n; i--) {
            if(dp[target]) return true;
            dp[i] = dp[i] || dp[i-n];
        }
    }
    return dp[target];
}
```



# 437 路径总和III

给定一个二叉树，它的每个结点都存放着一个整数值。

找出路径和等于给定数值的路径总数。

路径不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。

二叉树不超过1000个节点，且节点数值范围是 [-1000000,1000000] 的整数。

**示例**：

root = [10,5,-3,3,2,null,11,3,-2,null,1], sum = 8

          10
         /  \
        5   -3
       / \    \
      3   2   11
     / \   \
    3  -2   1
    
    返回 3。和等于 8 的路径有:
    
    1.  5 -> 3
    2.  5 -> 2 -> 1
    3.  -3 -> 11
**题解：**

``` javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val, left, right) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.left = (left===undefined ? null : left)
 *     this.right = (right===undefined ? null : right)
 * }
 */
/**
 * @param {TreeNode} root
 * @param {number} sum
 * @return {number}
 */
var pathSum = function(root, sum) {
    let res = 0;
    // 深度优先遍历
    const travel  = (s, root) => {
        if(root) {
            s += root.val;
            if(s === sum) {
                res++;
            }
            travel(s, root.left);
            travel(s, root.right);
            // 如果该节点的子节点未作为过起始节点
            // 以子节点为起始节点递归
            // visted->true
            if(!root.visted) {
                root.visted = true;
                travel(0, root.left);
                travel(0, root.right);
            }
        }
    }
    travel(0, root);
    return res;
};
```

