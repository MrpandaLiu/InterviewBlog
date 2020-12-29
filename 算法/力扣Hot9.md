# 438 找到字符串中所有字母异位词

给定一个字符串 s 和一个非空字符串 p，找到 s 中所有是 p 的字母异位词的子串，返回这些子串的起始索引。

字符串只包含小写英文字母，并且字符串 s 和 p 的长度都不超过 20100。

说明：

字母异位词指字母相同，但排列不同的字符串。
不考虑答案输出的顺序。
**示例**:

```
输入:
s: "cbaebabacd" p: "abc"

输出:
[0, 6]

解释:
起始索引等于 0 的子串是 "cba", 它是 "abc" 的字母异位词。
起始索引等于 6 的子串是 "bac", 它是 "abc" 的字母异位词。
```

**题解：**

``` javascript
/**
 * @param {string} s
 * @param {string} p
 * @return {number[]}
 */
var findAnagrams = function(s, p) {
    const res = [];
    let left = 0,right = 0;
    // 需求表， 窗口表
    const needs = {},windows = {};
    let match = 0;
    // 初始化需求表
    for(let i in p) {
        needs[p[i]]  = needs[p[i]] ? needs[p[i]]+1 : 1;
    }
    // 需求表的长度
    let needsLen = Object.keys(needs).length;
    while(right < s.length){
        // 获取当前字符
        let c1 = s[right];
        // 如果是需求表的字符
        if(needs[c1]){
            // 窗口表++
            windows[c1] = windows[c1] ? windows[c1]+1 :  1;
            // 如果对应字符数量相等 匹配成功+1
            if(windows[c1] === needs[c1]) match++;
        }
        // 右移扩大窗口
        right++;
        // 如果所有类型的字符都已经完全匹配
        while(match === needsLen){
            // 如果窗口的宽度和目标字符串的长度相等
            if(right - left === p.length) res.push(left);
            // 获取当前窗口左边界
            let c2 = s[left];
            // 如果在需求表内
            if(needs[c2]){
                // 窗口表--
                windows[c2]--;
                if(windows[c2] < needs[c2]){    
                    match--;
                }
            }
            // 右移缩小窗口
            left++;
        }
    }
    return res;
};
```



# 448 找到所有数组中消失的数字

给定一个范围在  1 ≤ a[i] ≤ n ( n = 数组大小 ) 的 整型数组，数组中的元素一些出现了两次，另一些只出现一次。

找到所有在 [1, n] 范围之间没有出现在数组中的数字。

您能在不使用额外空间且时间复杂度为O(n)的情况下完成这个任务吗? 你可以假定返回的数组不算在额外空间内。

**示例**:

```
输入:
[4,3,2,7,8,2,3,1]

输出:
[5,6]
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var findDisappearedNumbers = function(nums) {
    const hash = [];
    const res = [];
    // 记录出现过的hash数组中对应下标值为true
    for(let i=0; i<nums.length; i++) {
        hash[nums[i]] = true;
    }
    // 找到所有undefined的推入数组
    for(let i=1; i<=nums.length;i++) {
        if(!hash[i]) res.push(i);
    }
    return res;
};
```



# 461 汉明距离

两个整数之间的汉明距离指的是这两个数字对应二进制位不同的位置的数目。

给出两个整数 x 和 y，计算它们之间的汉明距离。

注意：
0 ≤ x, y < 231.

**示例**:

```
输入: x = 1, y = 4

输出: 2

解释:
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑

上面的箭头指出了对应二进制位不同的位置。
```

**题解：**

``` javascript
/**
 * @param {number} x
 * @param {number} y
 * @return {number}
 */
var hammingDistance = function(x, y) {
    // 异或后转化成二进制后转为数组统计0的个数
    return (x ^ y).toString(2).split('').filter(a => a === '1').length;
};
```



# 493 目标和

给定一个非负整数数组，a1, a2, ..., an, 和一个目标数，S。现在你有两个符号 + 和 -。对于数组中的任意一个整数，你都可以从 + 或 -中选择一个符号添加在前面。

返回可以使最终数组和为目标数 S 的所有添加符号的方法数。

 

**示例**：

```
输入：nums: [1, 1, 1, 1, 1], S: 3
输出：5
解释：

-1+1+1+1+1 = 3
+1-1+1+1+1 = 3
+1+1-1+1+1 = 3
+1+1+1-1+1 = 3
+1+1+1+1-1 = 3

一共有5种方法让最终目标和为3。
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @param {number} S
 * @return {number}
 */
var findTargetSumWays = function(nums, S) {
    let res = 0;
    // DFS 分加减两种情况
    // 当递归到最后一个数字 且 和目标值相等则+1
    const DFS = (op, idx, total) => {
        if(idx === nums.length) return;
        if(op === '+') total += nums[idx];
        else total -= nums[idx];
        if(total === S && idx === nums.length-1) res++;
        DFS('+', idx+1, total);
        DFS('-', idx+1, total);
    }
    DFS('+', 0, 0);
    DFS('-', 0, 0);
    return res;
};
```



# 538 把二叉搜索树转换为累加树

给出二叉 搜索 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 node 的新值等于原树中大于或等于 node.val 的值之和。

提醒一下，二叉搜索树满足下列约束条件：

节点的左子树仅包含键 小于 节点键的节点。
节点的右子树仅包含键 大于 节点键的节点。
左右子树也必须是二叉搜索树。
注意：本题和 1038: https://leetcode-cn.com/problems/binary-search-tree-to-greater-sum-tree/ 相同

 

**示例**：

![img538](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/05/03/tree.png)

输入：[4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
输出：[30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]

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
 * @return {TreeNode}
 */
var convertBST = function(root) {
    let total = 0;
    const travel = (root) => {
        if(!root) return null;
        //后序遍历
        if(root.right) travel(root.right);
        total += root.val;
        root.val = total;
        if(root.left) travel(root.left);
    }
    travel(root);
    return root;
};
```



# 543 二叉树的直径

给定一棵二叉树，你需要计算它的直径长度。一棵二叉树的直径长度是任意两个结点路径长度中的最大值。这条路径可能穿过也可能不穿过根结点。

 

**示例** :
给定二叉树

          1
         / \
        2   3
       / \     
      4   5    
返回 3, 它的长度是路径 [4,2,1,3] 或者 [5,2,1,3]。



**题解：**

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
var diameterOfBinaryTree = function(root) {
    if(!root) return 0;
    let res = 0;
    const dfs = (node) => {
        // 如果是叶子节点返回1
        if(!node.left && !node.right) return 1;
        // 获取左or右子树最大深度
        else {
            const left = node.left ? dfs(node.left) : 0;
            const right = node.right ? dfs(node.right) : 0;
            // 更新res
            if(left + right > res) res = left + right;
            // 返回左or右最深+自身的深度
            return Math.max(left, right)+1;
        }
    }
    dfs(root);
    return res;
};
```



# 560 和为K的子数组

给定一个整数数组和一个整数 k，你需要找到该数组中和为 k 的连续的子数组的个数。

**示例** :

```
输入:nums = [1,1,1], k = 2
输出: 2 , [1,1] 与 [1,1] 为两种不同的情况。
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number}
 */
const subarraySum = (nums, k) => {
  if (nums.length == 0) return 0;
  
  // 前缀和
  // 前j项和 - 前i项和 = i到j的和 
  const map = { 0: 1 };
  let prefixSum = 0;
  let count = 0;

  for (let i = 0; i < nums.length; i++) {
    prefixSum += nums[i];

    if (map[prefixSum - k]) {
      count += map[prefixSum - k];
    }

    map[prefixSum] = map[prefixSum] ? ++map[prefixSum] : 1;
  }
  return count;
};
```



# 581 最短无序连续子数组

给定一个整数数组，你需要寻找一个连续的子数组，如果对这个子数组进行升序排序，那么整个数组都会变为升序排序。

你找到的子数组应是最短的，请输出它的长度。

**示例**:

```
输入: [2, 6, 4, 8, 10, 9, 15]
输出: 5
解释: 你只需要对 [6, 4, 8, 10, 9] 进行升序排序，那么整个表都会变为升序排序。
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var findUnsortedSubarray = function(nums) {
    let res;
    // 拿到原数组副本
    const sortnum = nums.slice();
    // 新数组排序
    nums.sort((a,b) => a-b);
    let start=0,end=-1;
    // 分别从头尾对比 找到第一个不一样的数
    for(let i=0; i<nums.length;i++) {
        if(nums[i] !== sortnum[i]) {
            start = i;
            break;
        }
    }
    for(let i=nums.length; i>0;i--) {
        if(nums[i] !== sortnum[i]) {
            end = i;
            break;
        }
    }
    // 返回两个指针之差
    return end-start+1;
};
```



# 617 合并二叉树

给定两个二叉树，想象当你将它们中的一个覆盖到另一个上时，两个二叉树的一些节点便会重叠。

你需要将他们合并为一个新的二叉树。合并的规则是如果两个节点重叠，那么将他们的值相加作为节点合并后的新值，否则不为 NULL 的节点将直接作为新二叉树的节点。

**示例**:

```
输入: 
	Tree 1                     Tree 2                  
          1                         2                             
         / \                       / \                            
        3   2                     1   3                        
       /                           \   \                      
      5                             4   7                  
输出: 
合并后的树:
	     3
	    / \
	   4   5
	  / \   \ 
	 5   4   7
注意: 合并必须从两个树的根节点开始。
```

**题解：**

``` javascript
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} t1
 * @param {TreeNode} t2
 * @return {TreeNode}
 */
var mergeTrees = function(t1, t2) {
    let node = null;
    // 如果两棵树都存在
    // 创建新节点
    // 分别递归左右节点
    // 如果只有左 or 右 直接返回
    if(t1&&t2) {
       node = new TreeNode(t1.val+t2.val);
        node.left = mergeTrees(t1.left, t2.left);
        node.right = mergeTrees(t1.right, t2.right);
    } else if(t1) {
        return t1;
    } else if(t2) {
        return t2;
    }
    // 返回节点
    return node;
};
```



# 621 任务调度器

给你一个用字符数组 tasks 表示的 CPU 需要执行的任务列表。其中每个字母表示一种不同种类的任务。任务可以以任意顺序执行，并且每个任务都可以在 1 个单位时间内执行完。在任何一个单位时间，CPU 可以完成一个任务，或者处于待命状态。

然而，两个 相同种类 的任务之间必须有长度为整数 n 的冷却时间，因此至少有连续 n 个单位时间内 CPU 在执行不同的任务，或者在待命状态。

你需要计算完成所有任务所需要的 最短时间 。

 

**示例**：

```
输入：tasks = ["A","A","A","B","B","B"], n = 2
输出：8
解释：A -> B -> (待命) -> A -> B -> (待命) -> A -> B
     在本示例中，两个相同类型任务之间必须间隔长度为 n = 2 的冷却时间，而执行一个任务只需要一个单位时间，所以中间出现了（待命）状态。 
```

**题解：**

``` javascript
/**
 * @param {character[]} tasks
 * @param {number} n
 * @return {number}
 */
var leastInterval = function(tasks, n) {
//1.obj保存字母的出现次数
//2.max保存出现次数最多字母的次数
//3.maxcount保存出现次数最多字母的个数
//4.第一个for保存字母出现次数到obj
//5.第二个for记录max与count
//6.(max-1)(n+1)+maxcount，由于n表示间隔数，将每n+1个设为一组，一共有max-1组，比如ABCABCA，此时(max-1)(n+1)代表ABCABC，最后一组由出现次数最多字母的个数组成，即需要加上maxcount
//7.现在已经保存了出现次数最多字母的排序，如果将其他字母插入，可能长度会大于(max-1)*(n+1)+maxcount，所以取Math.max就好了

    var obj={},max=0,maxcount=0;
    for(let i=0;i<tasks.length;i++){
        obj[tasks[i]]=obj[tasks[i]]?obj[tasks[i]]+1:1
    }
    for(let i in obj){
        if(max<obj[i]){
            max=obj[i];
            maxcount=1;
        }else if(max==obj[i]){
            maxcount++;
        }
    }
    return Math.max((max-1)*(n+1)+maxcount,tasks.length)
};
```



# 647 回文子串

给定一个字符串，你的任务是计算这个字符串中有多少个回文子串。

具有不同开始位置或结束位置的子串，即使是由相同的字符组成，也会被视作不同的子串。

 

**示例 **：

````
输入："abc"
输出：3
解释：三个回文子串: "a", "b", "c"
````

**题解：**

``` javascript
/**
 * @param {string} s
 * @return {number}
 */
var countSubstrings = function(s) {
    const dp = new Array(s.length).fill(null).map(_ => []);
    let count = 0;
    // dp i,j代表i到j是否为回文串
    for(let i=s.length-1; i>=0; i--) {
        for(let j=s.length-1; j>=i; j--) {
            //i j相等 单字符为回文
            if(i===j) {
                dp[i][j] = true;
                count++;
            // 判断相邻的字符是否为回文
            } else if(j===i+1 && s[i]===s[j]) {
                dp[i][j] = true;
                count++;
            // 最两边的字符相等 且 中间为回文
            } else if(s[i]===s[j] && dp[i+1][j-1]) {
                dp[i][j] = true;
                count++;
            }
        }
    }
    return count;
};
```



# 739 每日温度


请根据每日 `气温` 列表，重新生成一个列表。对应位置的输出为：要想观测到更高的气温，至少需要等待的天数。如果气温在这之后都不会升高，请在该位置用 `0` 来代替。

例如，给定一个列表 `temperatures = [73, 74, 75, 71, 69, 72, 76, 73]`，你的输出应该是 `[1, 1, 4, 2, 1, 1, 0, 0]`。



**题解：**

``` javascript
/**
 * @param {number[]} T
 * @return {number[]}
 */
var dailyTemperatures = function(T) {
    // 使用单调栈
    const ret = new Array(T.length).fill(0);
    const stack = [];
    // 从右往左遍历
    for(let i=T.length-1; i>=0;i--) {
        // 如果当前的项 ＞ 栈顶的项 则栈顶项弹出
        while(stack.length && T[i] >= T[stack[stack.length - 1]]) {
            stack.pop();
        }
        // 计算距离
        if(stack.length) ret[i] = stack[stack.length - 1] - i;
        // 将当前项推入单调栈
        stack.push(i);
    }
    return ret;
}
```







