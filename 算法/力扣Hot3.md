<!--
 * @LastEditors: panda_liu
 * @LastEditTime: 2020-12-10 19:05:32
 * @FilePath: \yunniubaoc:\Users\23163\Desktop\web\Blog\算法\力扣Hot3.md
 * @Description: add some description
-->
# 33 搜索旋转排序数组

给你一个整数数组 nums ，和一个整数 target 。

该整数数组原本是按升序排列，但输入时在预先未知的某个点上进行了旋转。（例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] ）。

请你在数组中搜索 target ，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。

**示例**：
```
输入：nums = [4,5,6,7,0,1,2], target = 0
输出：4
```

**题解**

``` js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var search = function (nums, target) {
  if (!nums || nums.length === 0) return -1;
  let start = 0;
  let end = nums.length - 1;
  let mid;
  while (start <= end) {
    mid = Math.ceil((start + end) / 2);
    // 首尾中全部验证
    if (nums[mid] === target) return mid;
    if (nums[start] === target) return start;
    if (nums[end] === target) return end;
    // 说明前半部分有序
    if (nums[start] < nums[mid]) {
      // 说明目标值存在于有序部分，将末尾设置为mid
      // 继续执行二分查找
      if (nums[start] < target && target < nums[mid]) {
        end = mid - 1;
      } else {
        // 说明目标值存在于后半段
        start = mid + 1;
      }
    } else {
      // 说明后半部分有序
      // 判断目标值是否在后半部分
      if (nums[mid] < target && target < nums[end]) {
        start = mid + 1;
      } else {
        end = mid - 1;
      }
    }
  }
  return -1;
};
```

# 34 在排序数组中查找元素的第一个和最后一个位置

给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。

如果数组中不存在目标值 target，返回 [-1, -1]。

进阶：

你可以设计并实现时间复杂度为 O(log n) 的算法解决此问题吗？
 
**示例**：
```
输入：nums = [5,7,7,8,8,10], target = 8
输出：[3,4]
```

**题解**

``` js
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
var searchRange = function(nums, target) {
    // 初始化 设置max min为-1
    let start = 0,end = nums.length,min=-1,max=-1;
    let mid;

    const dfs = (idx) => {
        if(nums[idx]!==undefined && nums[idx] === target) {
            if(idx < min) {
                min = idx;
                dfs(idx-1);
            } else if(idx > max) {
                max = idx;
                dfs(idx+1);
            }
        }
    }
    // 二分法找到第一个目标数
    while(start <= end) {
        mid = Math.floor((start+end)/2);
        if(nums[mid] === target) {
            // 设置min max 如果nums只有一个target时，max和min输出同样的下标
            min=mid;
            max=mid;
            // 深度优先遍历两侧
            dfs(mid-1);
            dfs(mid+1);
            break;
        }
        // 二分
        // 如果mid的值小于target 则指针右移
        if(nums[mid] < target) start = mid+1;
        // 否则指针左移
        else end = mid-1;
    }

    return [min,max];
};
```

# 39 组合总和

给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。

candidates 中的数字可以无限制重复被选取。

说明：

所有数字（包括 target）都是正整数。
解集不能包含重复的组合。 
**示例**：
```
输入：candidates = [2,3,6,7], target = 7,
所求解集为：
[
  [7],
  [2,2,3]
]
```

**题解**

``` js
/**
 * @param {number[]} candidates
 * @param {number} target
 * @return {number[][]}
 */
var combinationSum = function(candidates, target) {
    // 先排序!!!
    candidates.sort((a,b) => a-b);
    const ret = [];
    // DFS
    const travel = (arr,idx, total) => {
        for(let i=idx;i<candidates.length;i++) {
            const temp = candidates[i];
            // 获取副本
            const narr = arr.slice(0);
            narr.push(temp);
            const res = total+temp;
            // 如果依然小于 则递归
            if(res < target) travel(narr,i, res);
            else if(res === target) ret.push(narr);
            else break;
        }
    }
    travel([],0, 0);
    return ret;
};
```

# 46 全排列

给定一个 没有重复 数字的序列，返回其所有可能的全排列。

**示例**
```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

**题解**
``` js
/**
 * @param {number[]} nums
 * @return {number[][]}
 */
// 回溯法
var permute = function(nums) {
    const ret = [];
    // 记录是否被访问过
    const used = {};
    // DFS
    const dfs = (arr) => {
        // 如果长度和目标长度相等 加入结果数组
        if(arr.length === nums.length) {
            ret.push(arr);
            return;
        }
        for(let i=0;i<nums.length;i++) {
            // 如果该数字被访问过 跳过
            if(used[nums[i]]) continue;
            // 添加到临时数组中
            arr.push(nums[i]);
            // 设置为已访问
            used[nums[i]] = true;
            // 继续深度遍历
            dfs(arr.slice());
            // 从临时数组弹出
            arr.pop();
            // 设置为未访问
            used[nums[i]] = false;
        }
    }
    dfs([]);
    return ret;
};
```

# 48 旋转图像

给定一个 n × n 的二维矩阵表示一个图像。

将图像顺时针旋转 90 度。

说明：

你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。

**示例**:
```
给定 matrix = 
[
  [1,2,3],
  [4,5,6],
  [7,8,9]
],

原地旋转输入矩阵，使其变为:
[
  [7,4,1],
  [8,5,2],
  [9,6,3]
]
```

**题解**
``` js
/**
 * @param {number[][]} matrix
 * @return {void} Do not return anything, modify matrix in-place instead.
 */
var rotate = function(matrix) {
    const n = matrix.length;
    // 先将下标为i j的元素变为j i
    for(let i=0;i<n;i++) {
        for(let j=i;j<n;j++) {
            [matrix[i][j],matrix[j][i]] = [matrix[j][i],matrix[i][j]];
        }
    }
    // 逆序
    for(let i=0;i<matrix.length;i++) {
        matrix[i].reverse();
    }
    return matrix;
};
```

# 49 字母异位词分组

给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。

**示例**:
```
输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
输出:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

**题解**

``` js
/**
 * @param {string[]} strs
 * @return {string[][]}
 */
var groupAnagrams = function(strs) {
    const result = [];
    // 收集字母组合类型map数组
    const map = [];
    for(let i=0;i<strs.length;i++) {
       // 重排序组合
        const standard = strs[i].split("").sort().join("");
        // 查找是否是新的组合
        const idx = map.indexOf(standard);
        // 如果是新的
        if(idx === -1) {
            // 在map中添加这一项
            map.push(standard);
            // 为该项在结果数组中创建新的
            const arr = [];
            arr.push(strs[i]);
            result.push(arr);
        } else {
            // 如果不是新的 直接加到对应下标的数组中
            result[idx].push(strs[i]);
        }
    }  
    return result;
};
```

# 53 最大子序和

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

**示例**:
```
输入: [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。
```

**题解**

``` js
/**
 * @param {number[]} nums
 * @return {number}
 */
var maxSubArray = function(nums) {
    let max=nums[0],total=0;
    // 顺序遍历
    for(let i=0; i<nums.length;i++) {
        // 如果前面和大于0 累加
        if(total >= 0) total+=nums[i];
        // 小于0 则让当前和等于目前的值
        else total = nums[i];
        // 比较大小
        max = total > max ? total : max;
    }
    return max;
}
```