---
title: 理论更优的 O (nk) 反而更慢？我在 LeetCode 上发现的一个反常识真相
date: '2026-03-05 11:08:57'
updated: '2026-03-05 11:23:14'
tags:
  - 算法
  - 数据结构与算法
  - leetcode
permalink: >-
  /post/is-the-theoretically-better-o-nk-slower-a-counterintuitive-truth-i-discovered-on-leetcode-zdaouu.html
author: Lu
---



# 理论更优的 O (nk) 反而更慢？我在 LeetCode 上发现的一个反常识真相

刷字母异位词分组这道题时，很多人都会遇到一个特别反直觉的现象：

书本上明明白白写着，计数法时间复杂度 O(nk)，严格优于排序法 O(nk log k)。  
可一上 LeetCode 提交，**排序版代码跑得反而更快**。

我一开始也以为是自己写得不够好，反复对比、优化之后才明白：  
这不是代码问题，是**理论复杂度和真实运行环境**之间的巨大差距。

---

## 你以为的优势，在短字符串面前不存在

算法复杂度看的是增长趋势，不看常数开销。  
但 LeetCode 里的测试用例，绝大多数字符串都特别短，长度大多在 20 以内。

在这种长度下：

- 排序 O(k log k) 实际只有几十次操作
- 计数 O(k) 虽然理论更优，却要多一轮统计、一轮拼接

两者的真实计算量几乎拉不开差距，复杂度优势直接被抹平。

更关键的是，**std::sort 是被优化到极致的库函数**。  
它不是简单的快排，而是快排、堆排、插入排序结合的 introsort，短序列直接走最快的插入排序，再加上编译器向量化、连续内存访问，CPU 缓存命中率拉满。

你手写的计数循环，再怎么精简，也很难打过几十年工程级优化的库函数。

---

## 计数法真正的坑：Key 拼接的隐形开销

排序法的 key 就是排序后的字符串，一段干净、连续的字符数组，哈希和比较都极快。

而常规计数法，大多是这样生成 key：

```cpp
string key;
for (int x : cnt) key += to_string(x) + "#";
```

这里面藏着巨大的开销：数字转字符串、字符串拼接、多次内存操作、非连续访问。

在字符串很短的场景里，**拼接 key 的开销，甚至超过排序本身**。  
你以为自己在 O(k)，其实是在 O(k + 巨大常数)。

再加上哈希表对长而零散的字符串 key 本身就不友好，一套组合下来，计数法自然慢了。

---

## 让计数法真正快起来：自定义哈希+数组Key

如果想让计数法发挥出 O(nk) 的理论优势，核心是**干掉字符串拼接**——直接用固定大小的数组做哈希表的 Key，搭配自定义哈希函数，彻底规避字符串操作的开销。

完整实现代码如下：

```cpp
#include <vector>
#include <string>
#include <unordered_map>
#include <array>

using namespace std;

// 自定义哈希函数：针对26位int数组，生成唯一哈希值
struct ArrayHash {
    size_t operator()(const array<int, 26>& arr) const {
        size_t hash = 0;
        for (int num : arr) {
            // 31是质数，减少哈希碰撞概率
            hash = hash * 31 + num;
        }
        return hash;
    }
};

class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        // Key直接用26位int数组，替代拼接的字符串
        unordered_map<array<int, 26>, vector<string>, ArrayHash> mp;
        mp.reserve(strs.size()); // 预分配内存，减少扩容开销

        for (const auto& s : strs) {
            array<int, 26> cnt = {0}; // 栈上数组，无内存分配开销
            for (char c : s) cnt[c - 'a']++;
            // 直接用数组做Key，无任何拼接操作
            mp[cnt].push_back(s);
        }

        vector<vector<string>> res;
        res.reserve(mp.size());
        for (auto& [_, vec] : mp) {
            res.push_back(move(vec)); // 移动语义，避免拷贝
        }
        return res;
    }
};
```

这种写法的核心优化点：

1. 用 `array<int,26>` 替代字符串作为 Key，彻底去掉字符串拼接、数字转字符串的开销；
2. 自定义哈希函数直接对数组计算哈希值，避免字符串哈希的额外消耗；
3. 数组是栈上连续内存，CPU 缓存友好，哈希计算和 Key 比较效率大幅提升。

这种优化后的计数法，在字符串长度中等（k>30）时，就能反超排序法；但是短字符串场景，仍然落后于排序法，无法彻底解决常规计数法“慢”的问题。

优化后的计数法只能在测试上达到8ms，而最快的排序法可以达到3ms，这主要就是对应的场景不同，所以要根据对应的场景选择对应的算法

---

## 那计数法什么时候才真的快？

只有字符串真的很长时（比如长度上百），排序的 log k 级别开销才会被彻底放大，计数法的线性优势才会体现出来。

但在 LeetCode 这道题里，几乎没有这种用例，所以常规场景下，排序版依然是“性价比最高”的选择——简洁、稳健、不易出错。

---

## 这道题真正教会我们的

这件事给我的启发特别简单：

复杂度是理论，常数项才是现实。  
手写算法不一定比库函数强，LeetCode 的运行速度，很多时候比的不是复杂度高低，而是**你有多懂机器、缓存、内存和标准库**。

所以下次再写这道题：

- 面试求稳，直接写排序版，简洁高效不出错；
- 想展示深度，再用“自定义哈希+数组Key”的计数版；
- 千万别写带字符串拼接的朴素计数法，又慢又啰嗦。

理论再漂亮，也要落地到真实机器上，才算真正懂算法。

‍
