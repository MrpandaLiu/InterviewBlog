# 142 环形链表II

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。注意，pos 仅仅是用于标识环的情况，并不会作为参数传递到函数中。

说明：不允许修改给定的链表。

进阶：

你是否可以使用 O(1) 空间解决此题？

**示例 **：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/07/circularlinkedlist.png)

```
输入：head = [3,2,0,-4], pos = 1
输出：返回索引为 1 的链表节点
解释：链表中有一个环，其尾部连接到第二个节点。
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
 * @return {ListNode}
 */
var detectCycle = function(head) {
    // 不断遍历
    while(head) {
        if(typeof head.val === "string")
            return head;
        
        head.val = String(head.val);
        head = head.next;
    }   
    return null;                               
};
```



# 146 LRU 缓存机制

运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制 。
实现 LRUCache 类：

LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前删除最久未使用的数据值，从而为新的数据值留出空间。


进阶：你是否可以在 O(1) 时间复杂度内完成这两种操作？

 

**示例**：

``` 
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

**题解**：

``` javascript
/**
 * @param {number} capacity
 */
var LRUCache = function(capacity) {
    this.length = capacity;
    this.keys = [];
    this.hash = {};
};

/** 
 * @param {number} key
 * @return {number}
 */
LRUCache.prototype.get = function(key) {
    if(!this.hash[key]) return -1;
    else {
        // 如果存在拿出来放到数组尾部
        const idx = this.keys.indexOf(key);
        this.keys.splice(idx, 1);
        this.keys.push(key);
        return this.hash[key];
    }
};

/** 
 * @param {number} key 
 * @param {number} value
 * @return {void}
 */
LRUCache.prototype.put = function(key, value) {
    // 不存在 添加
    if(!this.hash[key]) this.keys.push(key);
    //存在
    else {
        // 放在数组最后
        const idx = this.keys.indexOf(key);
        this.keys.splice(idx, 1);
        this.keys.push(key);
    }
    // 缓存该值
    this.hash[key] = value;
    // 如果长度超过最大长度 删除最久未使用
    if(this.keys.length > this.length) {
        const del = this.keys.shift();
        delete this.hash[del];
    } 
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * var obj = new LRUCache(capacity)
 * var param_1 = obj.get(key)
 * obj.put(key,value)
 */
```



#  148 排序链表

给你链表的头结点 head ，请将其按 升序 排列并返回 排序后的链表 。

进阶：

你可以在 O(n log n) 时间复杂度和常数级空间复杂度下，对链表进行排序吗？

**示例**：

```
输入：head = [4,2,1,3]
输出：[1,2,3,4]
```

**题解**：

``` javascript
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var sortList = function(head) {
    // hash用来存所有值
    const hash = [];
    let res = h = head;
    // 遍历获得所有值
    while(head) {
        hash.push(head.val);
        head = head.next;
    }
    // 对hash排序
    hash.sort((a,b) => a-b);
    // 按顺序赋值
    for(let i=0; i<hash.length; i++) {
        h.val = hash[i];
        h = h.next;
    }
    return res;
};
```



# 152 乘积最大子数组

给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

 

**示例**:

```
输入: [2,3,-2,4]
输出: 6
解释: 子数组 [2,3] 有最大乘积 6。
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxProduct = function(nums) {
    let res = nums[0];
    // 因为一个负数*一个负数可能得到最大值 所以我们记录每一步dp的最大和最小值
    let prevMin = nums[0];
    let prevMax = nums[0];
    let temp1 = 0, temp2 = 0;
    for(let i=1; i<nums.length; i++) {
        // 获得当前的两个值
        temp1 = prevMin*nums[i];
        temp2 = prevMax*nums[i];
        // 更新最大最小
        prevMin = Math.min(temp1, temp2, nums[i]);
        prevMax = Math.max(temp1, temp2, nums[i]);
        // 更新返回值
        res = prevMax > res ? prevMax : res;
    }
    return res;
};
```



# 155 最小栈

设计一个支持 push ，pop ，top 操作，并能在常数时间内检索到最小元素的栈。

push(x) —— 将元素 x 推入栈中。
pop() —— 删除栈顶的元素。
top() —— 获取栈顶元素。
getMin() —— 检索栈中的最小元素。

**示例**:

```
输入：
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

输出：
[null,null,null,null,-3,null,0,-2]

解释：
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.
```

**题解：**

``` javascript
/**
 * initialize your data structure here.
 */
var MinStack = function() {
    this.stack = [];
};

/** 
 * @param {number} x
 * @return {void}
 */
MinStack.prototype.push = function(x) {
    this.stack.push(x);
};

/**
 * @return {void}
 */
MinStack.prototype.pop = function() {
    this.stack.pop();
};

/**
 * @return {number}
 */
MinStack.prototype.top = function() {
    return this.stack[this.stack.length-1];
};

/**
 * @return {number}
 */
MinStack.prototype.getMin = function() {
    return Math.min(...this.stack);
};

/**
 * Your MinStack object will be instantiated and called as such:
 * var obj = new MinStack()
 * obj.push(x)
 * obj.pop()
 * var param_3 = obj.top()
 * var param_4 = obj.getMin()
 */
```



# 160 相交链表

编写一个程序，找到两个单链表相交的起始节点。

**示例** ：

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2018/12/14/160_example_1.png)

```
输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个链表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
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
 * @param {ListNode} headA
 * @param {ListNode} headB
 * @return {ListNode}
 */
var getIntersectionNode = function(headA, headB) {
    // 双指针遍历两个链表
    // B每遍历一轮 A往后走一个
    while(headA) {
        let h = headB;
        while(h) {
            if(headA == h) {
                return headA;
            } else {
                h = h.next;
            }
        }
        headA = headA.next;
    }
    return null;
};
```



# 169 多数元素

给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

 

**示例 **:

```
输入: [3,2,3]
输出: 3
```

**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var majorityElement = function(nums) {
    // 用hash记录遍历的每个数字的个数 如果大于一半直接返回
    const hash = [];
    let res;
    for(let i=0;i<nums.length;i++) {
        hash[nums[i]] = hash[nums[i]] ? ++hash[nums[i]] : 1;
        if(hash[nums[i]] > nums.length/2) res = nums[i];
    }
    return res;
};
```



# 198 打家劫舍

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

 

**示例 **：

```
输入：[1,2,3,1]
输出：4
解释：偷窃 1 号房屋 (金额 = 1) ，然后偷窃 3 号房屋 (金额 = 3)。
     偷窃到的最高金额 = 1 + 3 = 4 。
```



**题解：**

``` javascript
/**
 * @param {number[]} nums
 * @return {number}
 */
var rob = function(nums) {
    if(nums.length === 0)   return 0;
    const dp = [];
    // 初始化dp[0] dp[1]
    dp[0] = nums[0];
    dp[1] = Math.max(dp[0], nums[1]);
    // 动态规划 当前dp = dp上一个 与 dp前两个+当前值 的最大值
    for(let i=2;i<nums.length;i++) {
        dp[i] = Math.max(dp[i-1], dp[i-2]+nums[i]);
    }
    return dp[nums.length-1];
};
```



# 200 岛屿数量

给你一个由 '1'（陆地）和 '0'（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

 

**示例 **：

```
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

**题解：**

``` javascript
/**
 * @param {character[][]} grid
 * @return {number}
 */
var numIslands = function(grid) {
    let ret = 0;
    if(grid.length === 0) return 0;
    // 沉岛
    const delLand = (i,j) => {
        // 将当前陆地置为海
        grid[i][j] = '0';
        // 深度优先寻找四个方向 置为海
        if(grid[i-1] && grid[i-1][j] === '1') delLand(i-1,j);
        if(grid[i+1] && grid[i+1][j] === '1') delLand(i+1,j);
        if(grid[i][j+1] === '1') delLand(i,j+1);
        if(grid[i][j-1] === '1') delLand(i,j-1);
    }
    
    const n = grid.length,m = grid[0].length;
    for(let i=0;i<n;i++) {
        for(let j=0;j<m;j++) {
            // 遇到岛屿+1 然后沉岛
            if(grid[i][j] === '1') {
                ret++;
                delLand(i,j);
            }
        }
    }
    return ret;
};
```



#  206 反转链表

反转一个单链表。

**示例:**

```
输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL
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
 * @return {ListNode}
 */
var reverseList = function(head) {
    if(!head)   return null;
    let i = head;
    const travel = (cur, before) => {
        // 如果有next 继续递归 重置next
        if(cur.next)  {
            travel(cur.next, cur);
            cur.next = null;
        }
        // 如果没有证明到达最后一个节点 将它设置为头节点
        else i = cur;
        // 如果有before 反向设置next
        if(before) {
            cur.next = before;
        }
    }
    travel(head, null);
    return i;
};
```

