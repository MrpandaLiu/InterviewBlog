<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-11-23 11:08:38
 * @FilePath: \Blog\算法\剑指offer-1.md
 * @Description: add some description
-->
# 03 数组中重复的数字

找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

示例 1：

> 输入：
> [2, 3, 1, 0, 2, 5, 3]  
> 输出：2 或 3 

``` js
// hash法
var findRepeatNumber = function(nums) {
    // 使用hash记录每个值的出现次数
    const hash = [];
    for(let i=0;i<nums.length;i++) {
        hash[nums[i]] = hash[nums[i]] ? hash[nums[i]]+1 : 1;
        // 如果出现次数大于1 返回该值
        if(hash[nums[i]] === 2) return nums[i];
    }
    return -1;
};
```


# 05 替换空格

请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

示例 1：

> 输入：s = "We are happy."  
> 输出："We%20are%20happy."

``` js
var replaceSpace = function(s) {
    // 利用js中的split把字符串按空格拆分成数组，然后用%20拼接
    return s.split(" ").join("%20");
};
```

# 06 从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

示例 1：

> 输入：head = [1,3,2]  
> 输出：[2,3,1]

``` js
var reversePrint = function(head) {
    const res = [];
    while(head) {
        // 利用js数组中的unshift方法
        res.unshift(head.val);
        head = head.next;
    }
    return res;
};
```

# 07 重建二叉树

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

 

例如，给出

前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：

``` js
    3
   / \
  9  20
    /  \
   15   7
```
 
``` js
var buildTree = function(preorder, inorder) {
    // 如果数组为空返回null
    if(preorder.length === 0) return null;
    // 如果只有一项为叶子节点
    if(preorder.length === 1) return new TreeNode(preorder[0]);
    // 前序遍历数组的第一项即为根节点
    const proot = preorder.shift();
    // 获取中序遍历数组中根节点的索引
    const idx = inorder.indexOf(proot);
    // 根据索引获取中序遍历的根节点
    const iroot = inorder.splice(idx, 1);
    // 创建真实树的节点
    const root = new TreeNode(proot);
    // 前序数组从0到idx-1为左子树；中序数组从0-idx-1为右子树
    root.left = buildTree(preorder.slice(0, idx), inorder.slice(0, idx));
    // 前序数组从idx到结尾为右子树；中序数组从idx到结尾为右子树
    root.right = buildTree(preorder.slice(idx), inorder.slice(idx));
    return root;
};
```

# 09 用两个栈实现队列

太简单了，不放了

# 10-1 斐波那契数列（10-2 青蛙跳台阶同理）

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项。斐波那契数列的定义如下：

> F(0) = 0,   F(1) = 1  
> F(N) = F(N - 1) + F(N - 2), 其中 N > 1.  
斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。


示例 1：

> 输入：n = 2  
> 输出：1  

示例 2：  

> 输入：n = 5  
> 输出：5  

``` js
var fib = function(n) {
    // 边界判断
    if(n <= 1) return n;
    let pre = 0;
    let aft = 1;
    let sum;
    for(let i=2;i<=n;i++) {
        // 斐波那契数列中当前数 等于前两个数之和
        sum = pre + aft;
        if(sum >= 1000000007) sum = sum % 1000000007;
        // 更新两个指针指向新的值
        pre = aft;
        aft = sum;
    }
    return sum;
};
```

# 11 旋转数组的最小数字

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。  

示例 1：

> 输入：[3,4,5,1,2]  
> 输出：1  

示例 2：

> 输入：[2,2,2,0,1]  
> 输出：0  

``` js
var minArray = function(numbers) {
    // 边界条件直接返回
    if(numbers.length === 1) return numbers[0];
    let cur = 0;
    for(let i=0; i<numbers.length; i++) {
        // 如果递增，则前进指针
        if(numbers[cur] <= numbers[i]) cur = i;
        // 如果小于 且 没遍历到结尾 返回当前值
        else if(i<numbers.length) return numbers[i];
    }
    // 如果全程单调递减， 返回第一个值
    return numbers[0];
};
```

# 12 矩阵中的路径

请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。例如，在下面的3×4的矩阵中包含一条字符串“bfce”的路径（路径中的字母用加粗标出）。

``` js
[["a","b","c","e"],
["s","f","c","s"],
["a","d","e","e"]]
```

但矩阵中不包含字符串“abfb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入这个格子。

示例 1：

> 输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"  
> 输出：true  

``` js
var exist = function(board, word) {
    // 数组长度宽度
    const row = board.length;
    const col = board[0].length;
    const dfs = (i, j, idx) => {
        // 如果越界or不等于word对应字母 返回false
        if(i<0 || i>= row || j<0 || j>col || board[i][j] !== word[idx]) return false;
        // 如果到最后一个字母 直接返回true 
        if(idx === word.length-1) return true;
        // 将当前字母值存在tmp中， 锁住board[i][j]
        const tmp = board[i][j];
        board[i][j] = '/';
        // 四周有一个true即可
        const res = dfs(i-1,j,idx+1) || dfs(i,j-1,idx+1) || dfs(i,j+1,idx+1) || dfs(i+1,j,idx+1);
        // 恢复现场
        board[i][j] = tmp;
        // 返回结果
        return res;
    }

    // 遍历找到和word第一个字母相同的项
    for(let i=0; i<row; i++) {
        for(let j=0; j<col; j++) {
            if(dfs(i,j,0)) return true;
        }
    }
    return false;
};
```

# 13 机器人的运动范围
地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

示例 1：

> 输入：m = 2, n = 3, k = 1  
> 输出：3  

``` js
var movingCount = function(m, n, k) {
    // 设置是否访问过
    const visted = {};
    // 记录可以到达的格子个数
    let res = 0;
    // 通过reduce迭代相加看是否在k之内
    const canMov = (i, j, k) => {
        const val1 = i.toString().split("").reduce((total, cur)=>{ return total+= Number(cur)}, 0);
        const val2 = j.toString().split("").reduce((total, cur)=>{ return total+= Number(cur)}, 0);
        if(val1 + val2 <= k) return true;
        else return false;
    }
    const dfs = (i, j) => {
        // 越界直接return
        if(i<0 || j<0 || i>=m || j>=n) return;
        // 如果没有访问过 & 可以访问
        if(!visted[`${i}|${j}`] && canMov(i,j,k)) {
            // 设置已访问标记
            visted[`${i}|${j}`] = true;
            // 计数+1
            res++;
            // 深度遍历其他格子
            dfs(i-1, j);
            dfs(i+1, j);
            dfs(i, j-1);
            dfs(i, j+1);
        }
    }
    dfs(0,0);
    return res;
};
```