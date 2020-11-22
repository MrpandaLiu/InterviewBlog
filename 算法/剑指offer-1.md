<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-11-22 15:49:11
 * @FilePath: \undefinedc:\Users\23163\Desktop\web\Blog\算法\剑指offer-1.md
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