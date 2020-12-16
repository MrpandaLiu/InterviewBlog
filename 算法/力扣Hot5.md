# 101 对称二叉树

给定一个二叉树，检查它是否是镜像对称的。

 

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

    	1
       / \
      2   2
     / \ / \
    3  4 4  3

**题解**

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
var isSymmetric = function(root) {
    if(!root) return true;
    const travel = (left, right) => {
        // 条件1 左右子树都没有子节点 -》 true
        if(!left && !right) return true;
        else if(left && right) {
            // 条件2 左右子节点值相等
            if(left.val === right.val) {
                递归搜索左左和右右，左右和右左
                return travel(left.left,right.right) && travel(left.right,right.left);
            }
            // 值不等 -》 false
            else return false;
        } else return false;	// 两边节点不对应 -》false
    }
    return travel(root.left,root.right);
};
```



# 102 二叉树层次遍历

给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。



**示例**：
二叉树：[3,9,20,null,null,15,7],

    	3
       / \
      9  20
        /  \
       15   7
    返回其层次遍历结果：
    
    [
      [3],
      [9,20],
      [15,7]
    ]
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
 * @return {number[][]}
 */
var levelOrder = function(root) {
    if(!root) return [];
    const nodes = [];
    // 获取第一层
    const result = [[root.val]];
    // 层的下标
    let level = 0;
    nodes.push(root);
    while(nodes.length !== 0) {
        const arr = [];
        // 遍历上一层 创建下一层的数组
        for(let i=0;i<result[level].length;i++) {
            const cur = nodes.shift();
            if(cur.left) {
                nodes.push(cur.left);
                arr.push(cur.left.val);
            }
            if(cur.right) {
                nodes.push(cur.right);
                arr.push(cur.right.val);
            }
        }
        移动到下一层
        level++;
        if(arr.length) result.push(arr);
    }
    return result;
};
```



# 104 二叉树的最大深度



给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

**示例**：
给定二叉树 [3,9,20,null,null,15,7]，

    	3
       / \
      9  20
        /  \
       15   7
    返回它的最大深度 3 。


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
var maxDepth = function(root) {
    if(!root) return 0;
    // 初始化深度
    let maxDeep = 1;
    // 深度优先遍历
    const travel = (floor, node) => {
        // 如果大于当前最大深度
        if(floor > maxDeep) maxDeep = floor;
        // 如果左右有子节点，继续递归并且深度+1
        if(node.left) {
            travel(floor+1, node.left);
        }
        if(node.right) {
            travel(floor+1,node.right);
        }
    }
    travel(1,root);
    return maxDeep;
};
```



# 105 从前序与中序遍历序列构造二叉树

根据一棵树的前序遍历与中序遍历构造二叉树。

**注意**:
你可以假设树中没有重复的元素。

例如，给出

前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

    	3
       / \
      9  20
        /  \
       15   7
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
 * @param {number[]} preorder
 * @param {number[]} inorder
 * @return {TreeNode}
 */
var buildTree = function(preorder, inorder) {
    if(inorder.length === 0)    return null;
    // 前序遍历的第一个节点是每层子树的根节点
    const root = new TreeNode(preorder[0]);
    // 找到中序遍历的根节点
    const mid = inorder.indexOf(preorder[0]);
    // 递归左子树
    root.left = buildTree(preorder.slice(1, mid+1), inorder.slice(0 ,mid));
    // 递归右子树
    root.right = buildTree(preorder.slice(mid+1), inorder.slice(mid+1));
    return root;
};
```



# 114 二叉树展开为链表

给定一个二叉树，原地将它展开为一个单链表。

 

例如，给定二叉树

    	1
       / \
      2   5
     / \   \
    3   4   6
    
    将其展开为：
    1
     \
      2
       \
        3
         \
          4
           \
            5
             \
              6


**题解**：

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
 * @return {void} Do not return anything, modify root in-place instead.
 */
var flatten = function(root) {
    const arr = [];
    // 后序遍历 得到一个逆向数组
    const travel = (node) => {
        if(!node) return;
        if(node.right) travel(node.right);
        if(node.left) travel(node.left);
        arr.push(node);
    }
    travel(root);
    // 顺序弹出 设置左节点null 右节点为数组前一个节点
    let cur = arr.pop();
    while(arr.length) {
        cur.left = null;
        cur.right = arr.pop();
        cur = cur.right;
    }
};
```



# 121 买卖股票的最佳时机

给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。

如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。

注意：你不能在买入股票前卖出股票。

 

**示例** :

```
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
```

**题解**：

``` javascript
/**
 * @param {number[]} prices
 * @return {number}
 */
var maxProfit = function(prices) {
    if(prices.length === 0) return 0;
    let max = prices[0];
    let min = prices[0];
    let total = 0;
    for(let i=0; i<prices.length; i++) {
        if(prices[i] > max) max = prices[i];
        // 更新最小值后 同时需要重置最大值
        // 因为要在买股票之后卖
        if(prices[i] < min) {
            min = prices[i];
            max = min;
        }
        // 每次遍历更新最大利润
        total = total > max-min ? total : max-min;
    }
    return total;
};
```



# 136 只出现一次的数字

给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

说明：

你的算法应该具有线性时间复杂度。 你可以不使用额外空间来实现吗？

**示例** :

```
输入: [2,2,1]
输出: 1
```

**实现**：

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var singleNumber = function(nums) {
    let res = nums[0];
    for(let i=1;i<nums.length;i++) {
        // 两个相等的数异或结果为0
        // 0和任意的数异或结果为这个数本身
        res = res ^ nums[i];
    }
    return res;
};
```



# 139 单词拆分

给定一个非空字符串 s 和一个包含非空单词的列表 wordDict，判定 s 是否可以被空格拆分为一个或多个在字典中出现的单词。

说明：

拆分时可以重复使用字典中的单词。
你可以假设字典中没有重复的单词。
**示例**：

``` 
输入: s = "leetcode", wordDict = ["leet", "code"]
输出: true
解释: 返回 true 因为 "leetcode" 可以被拆分成 "leet code"。
```

**题解：**

``` javascript
/**
 * @param {string} s
 * @param {string[]} wordDict
 * @return {boolean}
 */
var wordBreak = function(s, wordDict) {
    const n = s.length;
    // 初始化dp
    const dp = new Array(n+1).fill(false);
    // 默认第一个字符为true
    dp[0] = true;
	// 双指针判断子串是否在字典中
    // 如果子串在字典中 且前面的dp为true 则说明到目前为止都为true
    for(let i=0; i<= n; i++) {
        for(let j=i-1;j>=0;j--) {
            const str = s.slice(j,i);
            if(wordDict.includes(str) && dp[j]) {
                dp[i] = true;
                break;
            }
        }
    }
    return dp[n];
};
```



# 141 环形链表

给定一个链表，判断链表中是否有环。

如果链表中有某个节点，可以通过连续跟踪 next 指针再次到达，则链表中存在环。 为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意：pos 不作为参数进行传递，仅仅是为了标识链表的实际情况。

如果链表中存在环，则返回 true 。 否则，返回 false 。

 

进阶：

你能用 O(1)（即，常量）内存解决此问题吗？

 

**示例 **：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

输入：head = [3,2,0,-4], pos = 1
输出：true
解释：链表中有一个环，其尾部连接到第二个节点。

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
var hasCycle = function(head) {
    // 设置快慢指针
    let slow= head;
    let fast = head;
    // 如果不是环可能会遍历到null
    while(slow && fast) {
        // 设置快慢
        slow = slow.next;
        if(fast.next) fast = fast.next.next;
        else return false;
        // 如果指向同一节点
        if(slow == fast) return true;
    }
    return false;
};
```

