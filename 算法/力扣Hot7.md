# 207 课程表

不会。。。



# 208 实现Trie（前缀树）


实现一个 Trie (前缀树)，包含 `insert`, `search`, 和 `startsWith` 这三个操作。

**示例:**

```
Trie trie = new Trie();

trie.insert("apple");
trie.search("apple");   // 返回 true
trie.search("app");     // 返回 false
trie.startsWith("app"); // 返回 true
trie.insert("app");   
trie.search("app");     // 返回 true
```

**题解:**

``` javascript
/**
 * Initialize your data structure here.
 */
var TrieNode = function (val) {
  // 当前节点的值
  this.val = val
  // 当前节点是否为某个单词的最后一个字母，若是，则代表是一个单词
  this.isWord = false
  // 子节点
  this.children = new Map()
}

var Trie = function() {
  this.root = new TrieNode(null)
};

/**
 * Inserts a word into the trie. 
 * @param {string} word
 * @return {void}
 */
Trie.prototype.insert = function(word) {
  let cur = this.root
  const len = word.length
  // 将单词插入到树中
  for (let i = 0; i < len; i++) {
    const char = word[i]
    if (!cur.children.has(char)) {
      cur.children.set(char, new TrieNode(char))
    }
    cur = cur.children.get(char)
    // 遍历到结尾的时候将 isWord 设为 true
    if (i === len - 1) {
      cur.isWord = true
    }
  }
};

/**
 * Returns if the word is in the trie. 
 * @param {string} word
 * @return {boolean}
 */
Trie.prototype.search = function(word) {
  let cur = this.root
  for (let char of word) {
    // 如果该字符不在 children 中，说明是新单词
    if (!cur.children.has(char)) {
      return false
    }
    cur = cur.children.get(char)
  }
  // 若顺利遍历完该单词，则进行判断该单词最后一个字母所在节点的 isWord 是否为真
  // 因为存在 key(未插入) / keyword(已插入) 这种情况
  return cur.isWord
};

/**
 * Returns if there is any word in the trie that starts with the given prefix. 
 * @param {string} prefix
 * @return {boolean}
 */
Trie.prototype.startsWith = function(prefix) {
  // 与 search 方法相比，该方法只需要判断能否成功遍历所有字符
  let cur = this.root
  for (let char of prefix) {
    if (!cur.children.has(char)) {
      return false
    }
    cur = cur.children.get(char)
  }
  return true
};
```



# 215 数组中第K个最大元素

在未排序的数组中找到第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

**示例**:

```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @param {number} k
 * @return {number}
 */
var findKthLargest = function(nums, k) {
    // 快速排序
    const quickSort = (arr) => {
        if(arr.length <= 1) return arr;
        const mid = Math.floor(arr.length/2);
        const pivot = arr.splice(mid,1)[0];

        const left = [];
        const right = [];

        for(let i=0;i<arr.length;i++) {
            if(arr[i] < pivot) left.push(arr[i]);
            else right.push(arr[i]);
        }
        return [...quickSort(left),pivot,...quickSort(right)];
    }
    const ret = quickSort(nums);
    // 从小到大排序 所以返回ret.length-k
    return ret[ret.length - k];
};
```



# 221 最大正方形

在一个由 '0' 和 '1' 组成的二维矩阵内，找到只包含 '1' 的最大正方形，并返回其面积。

 ![img](https://assets.leetcode.com/uploads/2020/11/26/max1grid.jpg)

**示例**：

```
输入：matrix = [["1","0","1","0","0"],["1","0","1","1","1"],["1","1","1","1","1"],["1","0","0","1","0"]]
输出：4
```

**题解：**

``` javascript
/**
 * @param {character[][]} matrix
 * @return {number}
 */
var maximalSquare = function(matrix) {
    let res = 0;
    if(matrix.length === 0) return res;
    let rows = matrix.length, cols = matrix[0].length;
    // 初始化dp
    let dp = new Array(rows).fill(null).map(() => new Array(cols).fill(0));
    // 初始化dp第一行
    for(let i=0; i<cols;i++) {
        if(matrix[0][i] === '0') dp[0][i] = 0;
        else {
            dp[0][i] = 1;
            res = 1;
        }
    }
    // 初始化dp第一列
    for(let i=0; i<rows; i++) {
        if(matrix[i][0] === '0') dp[i][0] = 0;
        else {
            dp[i][0] = 1;
            res = 1;
        }
    }
    // 如果当前为1
    // 当前最大 = 最小（左最大，上最大，左上最大）+1
    for(let i=1; i<rows;i++) {
        for(let j=1; j<cols; j++) {
            if(matrix[i][j] === '1') {
                dp[i][j] = Math.min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1]) + 1;
                res = Math.max(res, dp[i][j]);
            }
        }
    }
    return res*res;
};
```



# 226 翻转二叉树

翻转一棵二叉树。

**示例**：

输入：

         4
       /   \
      2     7
     / \   / \
    1   3 6   9

输出：

         4
       /   \
      7     2
     / \   / \
    9   6 3   1
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
 * @return {TreeNode}
 */
var invertTree = function(root) {
    if(!root) return null;
    const exchange = (node) => {
        // 如果为叶节点 返回
        if(!node.left && !node.right) {
            return;
        } else {
            // 左右节点交换
            const l = node.left;
            const r = node.right;
            node.left = r;
            node.right = l;
            //如果有左右子树 继续递归
            if(node.left) exchange(node.left);
            if(node.right) exchange(node.right);
        }
    }
    exchange(root);
    return root;
};
```



#  234 回文链表

请判断一个链表是否为回文链表。

**示例 1:**

```
输入: 1->2
输出: false
```

**示例 2:**

```
输入: 1->2->2->1
输出: true
```

**题解：**

``` javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @return {boolean}
 */
var isPalindrome = function(head) {
    let res = true;
    const arr = [];
    // 递归遍历
    const travel = (node) => {
        if(!node) return;
        // 推入数组
        //因为是递归遍历
        // 所以最后第一个数字和数组的第一项对比
        // 以此类推
        arr.push(node.val);
        travel(node.next);
        if(arr.shift() !== node.val) {
            res = false;
        }
    }
    travel(head);
    return res;
};
```



# 236 二叉树的最近公共祖先

给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

百度百科中最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

例如，给定如下二叉树:  root = [3,5,1,6,2,0,8,null,null,7,4]

![img236](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/15/binarytree.png)

**示例**:

```
输入: root = [3,5,1,6,2,0,8,null,null,7,4], p = 5, q = 1
输出: 3
解释: 节点 5 和节点 1 的最近公共祖先是节点 3。
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
 * @param {TreeNode} root
 * @param {TreeNode} p
 * @param {TreeNode} q
 * @return {TreeNode}
 */
var lowestCommonAncestor = function(root, p, q) {
    let broot = null; //公共父节点
    let dfs = function(root) { //递归遍历，返回找到pq的数量
        let count = 0; //记录找到几个
        if (root.val == p.val || root.val == q.val) {
            count++; //找到一个
        }
        if (!broot) { //还没有找到结果，继续找
            if (root.left) {
                count += dfs(root.left); //到左子树找，找到加上
            } 
            if (!broot && root.right) { //broot有值，证明已经找到了，没有值才继续找
                count += dfs(root.right); //到右子树找，找到加上
            } 
            if (!broot && count == 2) { //broot没有值，且找到2个了，证明是第一次找到
                broot = root; //递归是从叶子往上返回结果的，所以第一次找到的是最近的
            }
        }
        return count;
    }
    dfs(root);
    return broot;
};
```



# 238 除自身以外数组的乘积

给你一个长度为 n 的整数数组 nums，其中 n > 1，返回输出数组 output ，其中 output[i] 等于 nums 中除 nums[i] 之外其余各元素的乘积。

 

**示例**:

```
输入: [1,2,3,4]
输出: [24,12,8,6]
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {number[]}
 */
var productExceptSelf = function(nums) {
    if(nums.length < 2) return nums;
    // 初始化左右dp数组
    const dpL = new Array(nums.length).fill(1);
    const dpR = new Array(nums.length).fill(1);
    const res = [];
    // 初始化乘积
    let mulL = 1;
    let mulR = 1;
    // 两次遍历分别得到每一项左右累乘积
    for(let i=0; i<nums.length; i++) {
        mulL *= nums[i];
        dpL[i] = mulL;
    }
    for(let i=nums.length-1; i>0; i--) {
        mulR *= nums[i];
        dpR[i] = mulR;
    }
    // 第一个数=第二个数开始的乘积
    // 所以= dpR[1]
    res.push(dpR[1]);
    // 后面的每一项= 左边的乘积*右边的乘积
    for(let i=1; i<nums.length-1; i++) {
        res.push(dpL[i-1]*dpR[i+1]);
    }
    res.push(dpL[nums.length-2]);
    return res;
};
```



# 240 搜索二维矩阵

待完善



# 279 完全平方数

给定正整数 n，找到若干个完全平方数（比如 1, 4, 9, 16, ...）使得它们的和等于 n。你需要让组成和的完全平方数的个数最少。

**示例**:

```
输入: n = 12
输出: 3 
解释: 12 = 4 + 4 + 4.
```

**题解**：

``` javascript
/**
 * @param {number} n
 * @return {number}
 */
var numSquares = function(n) {
	const dp = [...Array(n+1)].map(_ => 0);
    for(let i=0; i<=n; i++) {
        // 最差的情况就是每次+1
        dp[i] = i;
        for(let j=1; i-j*j>=0; j++) {
            // 如果是完全平方数就是1次
            // 状态转移方程
            dp[i] = Math.min(dp[i], dp[i-j*j]+1)
        }
    }
    return dp[n];
};
```



# 283 移动零

给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

**示例**:

```
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]
```

**题解**：

``` javascript
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var moveZeroes = function(nums) {
    let n = nums.length;
    let i = 0;
    // 遍历数组
    while(n) {
        // 如果碰到0
        // 数组末尾push 同时剪切
        if(nums[i] === 0) {
            nums.push(0);
            nums.splice(i,1);
            i--;
        }
        // 移动下标
        i++;
        n--;
    }
};
```

