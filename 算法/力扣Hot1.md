<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-12-02 23:51:58
 * @FilePath: \undefinedc:\Users\23163\Desktop\web\Blog\算法\力扣Hot1-10.md
 * @Description: add some description
-->
# 1 两数之和

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

**示例:**
```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```
**题解**
``` js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var twoSum = function(nums, target) {
  // 双指针
  for(let i=0; i<nums.length-1;i++) {
    for(let j=i+1; j<nums.length;j++) {
      if(target === nums[i] + nums[j]) return [i, j];
    }
  }
}
```

# 2 两数相加

给出两个 非空 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 逆序 的方式存储的，并且它们的每个节点只能存储 一位 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例**：
```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```
**题解**：
``` js
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var addTwoNumbers = function(l1, l2) {
  let list = new ListNode(0);
  const head = list;
  let count = 0;
  while(l1 && l2) {
    // 如果l1有值就加
    if(l1) {
      list.val += l1.val;
      l1 = l1.next;
    }
    // 如果l2有值就加
    if(l2) {
      list.val += l2.val;
      l2 = l2.next;
    }
    // 加上进位
    list.val += count;
    // 判断值是否溢出 溢出则进位
    if(list.val >= 10) {
      list.val -= 10;
      count = 1;
    } else {
      count = 0;
    }
    // 如果临界
    if(!l1 && !l2) {
      // 没有进位 next为空
      if(count === 0) list.next = null;
      // 有进位 next为值=1的节点
      else list.next = new ListNode(1);
    } else {
      // 创建新节点 指针向后移
      list.next = new ListNode(0);
      list = list.next;
    }
  }
  return head;
}
```

# 3 无重复字符的最长子串

给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

**示例**
``` 
输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**题解**

``` js
/**
 * @param {string} s
 * @return {number}
 */
var lengthOfLongestSubstring = function(s) {
    // 临界判断
    if(s.length === 0) return 0;
    let res = 0;
    let str = "", j=0;
    // 双指针法
    while(j<s.length) {
        // 如果是不重复字符
        if(str.indexOf(s[j]) === -1) {
            // 添加到子串内
            str += s[j];
            // 尾指针+1
            j++;
            // 判断是否大于max
            res = str.length > res ? str.length : res;
        } else {  // 如果是重复子串
            // 头指针+1
            str = str.slice(1);
        }
    }
    return res;
};
```

# 5 最长回文子串

给定一个字符串 s，找到 s 中最长的回文子串。你可以假设 s 的最大长度为 1000。

**示例**
```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

**题解**
``` js
var longestPalindrome = function(s) {
    if(s.length <= 1) return s;
    let res = "";
    // 判断是否回文串
    const isPalindrome = (i, j) => {
        for(let idx=0; idx<=j-i; idx++) {
            if(s[i+idx]!==s[j-idx]) return false;
        }
        return true;
    }
    // 双指针 从大到小遍历
    for(let i=0; i<s.length-1; i++) {
        for(let j=s.length-1; j>=i;j--) {
            // 如果是回文串
            if(isPalindrome(i,j)) {
                const sub = s.slice(i, j+1);
                // 临界判断
                if(i===0 && j===s.length-1) return sub;
                // 如果大于当前最大 替换
                if(sub.length > res.length) {
                    res = sub;
                    // 因为范围会越缩越小 找到第一个后直接跳出
                    break;
                }
            }
        }
    }
    return res;
};
```

