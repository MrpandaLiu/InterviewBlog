<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-12-08 10:22:00
 * @FilePath: \yunniubaoc:\Users\23163\Desktop\web\Blog\算法\力扣Hot11-20.md
 * @Description: add some description
-->
# 11 盛最多水的容器

给你 n 个非负整数 a1，a2，...，an，每个数代表坐标中的一个点 (i, ai) 。在坐标内画 n 条垂直线，垂直线 i 的两个端点分别为 (i, ai) 和 (i, 0) 。找出其中的两条线，使得它们与 x 轴共同构成的容器可以容纳最多的水。

说明：你不能倾斜容器。

**题解**
``` js
var maxArea = function(height) {
    // 双指针 从两端向中间
    let res = 0,i=0,j=height.length-1,h;
    while(i<j) {
        // 找到两边较矮的作为高度
        h = height[i] > height[j] ? height[j] : height[i];
        // 判断最大是否更新
        res = res < h * (j - i) ? h * (j - i) : res;
        // 小的边一端移动指针
        if(height[j] >= height[i]) i++;
        else j--;
    }
    return res;
};
```

# 15 三数之和

给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。

注意：答案中不可以包含重复的三元组。

**示例**  
```
给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

**题解**
``` js
var threeSum = function(nums) {
    // 先排序
    nums=nums.sort((a,b) => a-b);
    let i = 0;
    let result = [];
    // 外层移动头指针
    while(i<nums.length-1){
      let a=nums[i],j=i+1,k=nums.length-1;
      //第二个指针
    	while(j<k){
    		var b=nums[j],c=nums[k];
    		var sum=a+b+c;
    		if(sum==0) result.push([a,b,c]);//存起来
    		if(sum<=0)
                while(nums[j]==b&&j<k)
                    j++;//第2个数
    		if(sum>=0)
                while(nums[k]==c&&j<k)
                    k--;//最后一个数
    	}
    	while(nums[i]==a&&i<nums.length-1)i++;//第一个数
    }
    return result;
};
```

# 17 电话号码的字母组合

给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

[图]

**示例**
```
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

**题解**
``` js
var letterCombinations = function(digits) {
    const nums = ["abc","def","ghi","jkl","mno","pqrs","tuv","wxyz"];
    if(!digits) return [];
    const ret = [];
    // 深度优先遍历
    const travel = (str,idx) => {
        // 遍历输入字符
        for(let i=0;i<nums[digits[idx]-2].length;i++) {
            // 获取当前字符串
            const temp = str+nums[digits[idx]-2][i];
            // 如果长度足够 加到结果数组
            if(idx === digits.length - 1) ret.push(temp);
            // 长度不足继续遍历
            else travel(temp,idx+1);
        }
    }; 
    travel("",0);
    return ret;
};
```

# 19 删除链表的倒数第N个节点

给定一个链表，删除链表的倒数第 n 个节点，并且返回链表的头结点。

**示例**
```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**题解**
``` js
/**
 * @param {ListNode} head
 * @param {number} n
 * @return {ListNode}
 */
var removeNthFromEnd = function(head, n) {
    // 节点数组
    const idxs = [];
    let h = head;
    // 遍历一遍收集节点
    while(head) {
        idxs.push(head);
        head = head.next;
    }
    // 获取要删除的节点的前一个节点下标
    const i = idxs.length - n - 1;
    // 如果下标为-1 说明删除的是头节点
    if(i === -1) {
        // 如果链表长度为一 返回null
        if(idxs.length === 1) return null;
        // 否则返回链表第二个节点
        else h = h.next;
    }
    // 如果是中间节点 将next指针后移
    else idxs[i].next = idxs[i].next.next;
    return h;
};
```

# 20 有效的括号

给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。

有效字符串需满足：

左括号必须用相同类型的右括号闭合。
左括号必须以正确的顺序闭合。
注意空字符串可被认为是有效字符串。

**示例**:
```
输入: "()"
输出: true
```

**题解**
``` js
var isValid = function(s) {
    const stack = [];
    for(let i=0;i<s.length;i++) {
        // 如果是左括号，添加到栈内
        if(['(','{','['].includes(s[i])) stack.push(s[i]);
        // 如果是右括号 匹配
        else {
            // 匹配成功继续
            // 匹配失败则返回false
            switch(s[i]) {
                case ")":
                    if(stack[stack.length-1] === "(") stack.pop();
                    else return false;
                    break;
                case "}":
                    if(stack[stack.length-1] === "{") stack.pop();
                    else return false;
                    break;  
                case "]":
                    if(stack[stack.length-1] === "[") stack.pop();
                    else return false;
                    break;              
            }
        }
    }
    // 如果栈长度为0 证明完全匹配完毕
    if(stack.length !== 0) return false;
    return true;
};
```

# 21 合并两个有序链表

将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**示例**
```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

**题解**

``` js
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var mergeTwoLists = function(l1, l2) {
    // 临界判断
    if(!l1) return l2;
    if(!l2) return l1;
    if(!l1 && !l2) return null;
    let ret,head;
    // 找排序后链表的头
    if(l1.val >= l2.val) {
        ret = head = l2;
        l2 = l2.next;
    } else {
        ret = head = l1;
        l1 = l1.next;
    }
    // 双指针遍历两个链表
    while(l1 && l2) {
        if(l1.val >= l2.val) {
            // 头指针重置next
            head.next = l2;
            //后移
            head = head.next;
            // 原指针后移
            l2 = l2.next;
        } else {
            head.next = l1;
            head = head.next;
            l1 = l1.next;
        }
    }
    // 如果原链表还有后续链表 直接接到目标链表之后
    if(l1) head.next = l1;
    if(l2) head.next = l2;
    return ret;
};
```

# 22 括号生成

数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。

**示例**：
```
输入：n = 3
输出：[
       "((()))",
       "(()())",
       "(())()",
       "()(())",
       "()()()"
     ]
```

**题解**

``` js
var generateParenthesis = function(n) {
    // 从左到右依次生成括号
    // 将符合条件的字符串添加到结果数组
    const res = [];
    const dfs = (s, left, right) => {
        if(left===n && right===n)   return res.push(s);
        // 左括号小于目标数量，深度优先遍历
        if(left < n) dfs(s+"(", left+1, right);
        // 右括号数量小于左括号 DFS右括号
        if(right < left) dfs(s+")", left, right+1);
    }
    dfs('', 0, 0);
    return res;
};
```

# 31 下一个排列

实现获取 下一个排列 的函数，算法需要将给定数字序列重新排列成字典序中下一个更大的排列。

如果不存在下一个更大的排列，则将数字重新排列成最小的排列（即升序排列）。

必须 原地 修改，只允许使用额外常数空间。

 

**示例**：
```
输入：nums = [1,2,3]
输出：[1,3,2]
```

**题解**

``` js
/**
 * @param {number[]} nums
 * @return {void} Do not return anything, modify nums in-place instead.
 */
var nextPermutation = function(nums) {
  let len = nums.length;
  if (len <= 1) return;

  // 从右到左遍历
  for (let i = len - 2; i >= 0; i--) {
    // 找到第一个左边小于右边的
    if (nums[i] < nums[i + 1]) {
        // 从右到左 找到比左边值大的交换
      for (let j = len - 1; j > i; j--) {
        if (nums[i] < nums[j]) {
          swap(i, j, nums)
          break;
        }
      }
      // 将右边的按大小交换
      let x = i + 1, y = len - 1;
      while (x < y) swap(x++, y--, nums)
      break;
    }
    // 如果逆序排列
    if (i === 0) {
      let x = i, y = len - 1;
      while (x < y) swap(x++, y--, nums)
    }
  }
};

function swap(i, j, nums) {
  let t = nums[i];
  nums[i] = nums[j];
  nums[j] = t;
}
```