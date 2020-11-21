<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-11-21 22:51:54
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


