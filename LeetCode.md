
# LeetCode

---
[toc]

---

1. 正则表达式
2. 执行时间过长，内存消耗过大。是否存在时间复杂度和空间复杂度更小的算法。
3. 不能将垃圾值与整数作比较，否则会报错。
4. 在遍历数组时一定要小心数组是否发生**越界**。
5. while循环切记更新循环变量。
6. [VS2017常用快捷键](https://www.cnblogs.com/seamusopen/p/8448646.html)

## 滑动窗口法

> 该算法展示了如何将嵌套for循环在一些问题中转换为单个for循环，从而降低时间复杂度。

1. 给一组大小为n的整数数组，**计算长度为k的子数组的和的最大值、最小值、XOR、乘积**。
2. 子字符串问题(滑动窗口题目)
   * (3)无重复字符的最长子串
   * (30)串联所有单词的子串
   * (159)至多包含两个不同字符的最长子串
   * (209)长度最小的子数组
   * (239)滑动窗口最大值
   * (567)字符串的排列
   * (632)最小区间
   * (727)最小窗口子序列

## 动态规划求解

1. **重点：**
   * 定义状态
   * 找到状态转移方程
2. **问题：**
   * Max/Min
   * Yes/No
   * Count(*)
   * Can't sort/swap
3. **四要素：**
   * 状态 state
   * 初始化 init
   * 方程 function
   * 结果 result
4. **常见类型**
   * 矩阵DP
   * 序列DP
   * 双序列 DP
   * 背包问题
