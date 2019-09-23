---
layout: post
title:  "All SubString Appear Once More"
date:   2019-9-20 12:00:00
categories: Algorithm
tags: LeetCode Cpp BitOperation SubString
permalink: /archivers/SubString10
comments: true
---
## 描述
所有 DNA 由一系列缩写为 A，C，G 和 T 的核苷酸组成，例如：`ACGAATTCCG`.

在研究 DNA 时，识别 DNA 中的重复序列有时会对研究非常有帮助.

编写一个函数来查找 DNA 分子中**所有出现超多一次的10个字母长的序列（子串）**.
<!--more-->
## 示例

输入: `s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"`

输出: `["AAAAACCCCC", "CCCCCAAAAA"]`

## 分析

**查找子串，这个子串长度为10，且在原字符串中出现超过一次.**

## 代码

以下为评论区代码，使用的**位运算**，很强👍

*做一个简要记录.*

***
4 个字符需要 2 个 bit 表示，一个长度为 10 的子串则需要 20 个bit. 例如
> A: 00, B: 01, C: 10, D: 11

20 个 bit 能表示的数值在 `0 ~ (1 << 20 - 1)` 之间.

则定义一个 `(1 << 20)` 长度的 bitset. 不妨记为`S`. 
即通过 `S` 就可以表示出所有长度为 10 的子串了. 

例如，对于子串`AAAAAAAABB`，20 位的 bit 显然为`0000 0000 0000 0000 0101`.
值为 5.
即对应 `S` 低 5 位为 **1**.

依此，每次顺位读一个长度为 10 的子串. 
若 `S1` 对应**位的值为 0** (`s1.test(val) == False`)，即该子串是第一次出现，
置该位值为 1 (`s1.set(val)`)；
否则，是第二次出现，记录该子串.

另外，可以再用一个 bitset 变量 `S2` 来记录已出现两次的子串，加快遍历速度.



```cpp
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        // 对应二进制00, 01, 10, 11. 那么10个组合只要20位就够了.
        unordered_map<char, int> m{ {'A', 0}, {'C', 1}, {'G', 2}, {'T', 3} };
        vector<string> res;
        bitset<1 << 20> s1, s2; //那么所有组合的值将在0到(1 << 20 - 1)之间
        int val = 0, mask = (1 << 20) - 1; //mask等于二进制的20个1
        // 类似与滑动窗口先把前10个字母组合
        for (int i = 0; i < 10; ++i) val = (val << 2) | m[s[i]];
        s1.set(val); //置位
        for (int i = 10; i < s.size(); ++i) {
            val = ((val << 2) & mask) | m[s[i]]; //去掉左移的一个字符再加上一个新字符
            if (s2.test(val)) continue; //出现过两次跳过
            if (s1.test(val)) {
                res.push_back(s.substr(i - 9, 10));
                s2.set(val);
            }
            else s1.set(val);
        }
        return res;
    }
};
```

## 参考
[LeetCode-187. 重复的DNA序列](https://leetcode-cn.com/problems/repeated-dna-sequences/)