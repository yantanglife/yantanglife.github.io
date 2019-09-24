---
layout: post
title:  "Split Array Largest Sum"
date:   2019-9-23 12:00:00
categories: Algorithm
tags: LeetCode Cpp Dichotomy
permalink: /archivers/LargestSum
comments: true
---
## 描述
给定一个非负整数数组和一个整数 m，你需要将这个数组分成 m 个非空的连续子数组.

设计一个算法使得这 m 个子数组各自和的最大值最小.
<!--more-->

**注意:**

数组长度 n 满足以下条件:

* 1 ≤ n ≤ 1000
* 1 ≤ m ≤ min(50, n)

## 示例

**输入:**

nums = [7, 2, 5, 10, 8]

m = 2

**输出:**

18

**解释:**

一共有四种方法将 nums 分割为 2 个子数组.

其中最好的方式是将其分为 [7, 2, 5] 和 [10, 8]，
因为此时这两个子数组各自的和的最大值为 18，在所有情况中最小.

## 分析

**二分**

~~*不会，答案还没看懂*.~~    😤

**先简单梳理一下**

`low` 初值为 `max(nums)`，`high` 初值为 `sum(nums)`.

`mid = (low + high) / 2`，显然不是 `nums` 中的数.

函数 `left(nums, target, m)`，其功能是对于给定的 `target=mid`，
遍历 `nums`，当第一个子数组的累和大于 `target` 时，往后接着计算第二个子数组的累和，
直到其值再次大于 `target`，依此，如果这样的子数组个数达到了 `m` 个，返回 `False`，
即表示 `target` 的值似乎是取小了，`target` 的值应该要更大一些才对.
反之，返回 `True`，则表示 `target` 的值需要取小一些.

与之相对应的则是更新 `low` 和 `high`.
若 `target=mid` 的值偏小，更新 `low = mid + 1`；
否则，更新 `high = mid`. 

直到 `low = high`，而**该值即为所求**.

***

**咦，梳理完好像就懂了**   😅

`low` 和 `high` 确定了分割方案中数组累和最大值的取值范围.

而最佳分割方案的值是确定的.

现在要做的就是采用二分法不断地缩小这个取值范围.

取累和最大值为 `mid`， 通过函数 `left()`，
来判断这个值是大是小，以此缩小取值范围，迭代计算，直到最后得到结果.

*不过，在 `left()` 中有一个疑点，当 `total > target` 时，需要重置 `total`，
为什么是 `total=e`，而不是 `total=0`？*

`total > target`，当前子数组 `[..., e]` 超过了限制，
需要从当前元素 `e` 开始重新计算一个新的子数组 `[e, ...]`.

### 算法复杂度

**时间复杂度为**
*`O(n∗log(sum of array))`*

二分搜索的时间复杂度为 *`O(log(sum of array))`*，
其中 *`sum of array`* 是 *`nums`* 中所有元素之和.
每次计算需要遍历一遍 *`nums`*，时间复杂度为 *`O(n)`*.

**空间复杂度**为 *`O(1)`*

## 代码
```cpp
class Solution {
public:
    int splitArray(vector<int>& nums, int m) {
        long lo = 0, hi = 0;
        // lo 为 nums 数组中的最大值，hi 为 nums 累和.
        for (auto e : nums) {
            lo = max(lo, (long)e);
            hi += e;
        }
        
        if (nums.size() == 1) return hi;
        if (nums.size() == m) return lo;
        
        while (lo < hi) {
            int mid = lo + (hi - lo >> 1);
            if (left(nums, mid, m)) { // 是否需要向左猜测
                hi = mid;
            } else {
                lo = mid + 1;
            }
        }
        return lo;
    }
    
    bool left(vector<int>& nums, int target, int m) {
        int count = 1, total = 0;
        for (auto e : nums) {
            total += e;
            if (total > target) {
                ++count;
                if (count > m) {
                    return false;
                }
                total = e;
            }
        }
        return true;
    }
};
```

## 参考
[LeetCode-410. 分割数组的最大值](https://leetcode-cn.com/problems/split-array-largest-sum/)