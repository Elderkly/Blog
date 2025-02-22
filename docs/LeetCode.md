# LeetCode

## 数制

数制类的题目多半跟数学有关，这类题目每隔一段时间要回顾一下，不然容易遗忘。
| 题目 | 难度 | 类型 | 标注 |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :--- | :---------------- | :----------------------------------- |
| [1837.K 进制表示下的各位数字总和](./LeetCode%E9%A2%98%E8%A7%A3/1837.K%E8%BF%9B%E5%88%B6%E8%A1%A8%E7%A4%BA%E4%B8%8B%E7%9A%84%E5%90%84%E4%BD%8D%E6%95%B0%E5%AD%97%E6%80%BB%E5%92%8C.md) | 简单 | 进制求和 | 重点 参考进制转换代码 |
| [504.七进制数](./LeetCode%E9%A2%98%E8%A7%A3/504.%E4%B8%83%E8%BF%9B%E5%88%B6%E6%95%B0.md) | 简单 | 进制转换 | 可将进制转换模板背下来 |
| [405.数字转换为十六进制数 负数采用补码表示](./LeetCode%E9%A2%98%E8%A7%A3/405.%E6%95%B0%E5%AD%97%E8%BD%AC%E6%8D%A2%E4%B8%BA%E5%8D%81%E5%85%AD%E8%BF%9B%E5%88%B6%E6%95%B0%E8%B4%9F%E6%95%B0%E9%87%87%E7%94%A8%E8%A1%A5%E7%A0%81%E8%A1%A8%E7%A4%BA.md) | 简单 | 进制转换 数制转换 | 忘记补码原码的转换规则就去看数据结构 |
| [面试题 16.01 不额外使用空间交换数组元素](./LeetCode%E9%A2%98%E8%A7%A3/16.01.%E4%B8%8D%E9%A2%9D%E5%A4%96%E4%BD%BF%E7%94%A8%E7%A9%BA%E9%97%B4%E4%BA%A4%E6%8D%A2%E6%95%B0%E7%BB%84%E5%85%83%E7%B4%A0.md) | 简单 | 异或 | 看到不使用额外空间要想到异或 |
| [201. 数字范围按位与 给定区间找出区间内所有数字按位相与的结果](./LeetCode%E9%A2%98%E8%A7%A3/201.%E6%95%B0%E5%AD%97%E8%8C%83%E5%9B%B4%E6%8C%89%E4%BD%8D%E4%B8%8E%E7%BB%99%E5%AE%9A%E5%8C%BA%E9%97%B4%E6%89%BE%E5%87%BA%E5%8C%BA%E9%97%B4%E5%86%85%E6%89%80%E6%9C%89%E6%95%B0%E5%AD%97%E6%8C%89%E4%BD%8D%E7%9B%B8%E4%B8%8E%E7%9A%84%E7%BB%93%E6%9E%9C.md) | 普通 | 移位 相与操作 | |
| [537. 复数乘法](./LeetCode%E9%A2%98%E8%A7%A3/537.%20%E5%A4%8D%E6%95%B0%E4%B9%98%E6%B3%95.md) | 普通 | 复数 | |
|[190. 颠倒二进制位 0100 => 0010](./LeetCode%E9%A2%98%E8%A7%A3/190.%20%E9%A2%A0%E5%80%92%E4%BA%8C%E8%BF%9B%E5%88%B6%E4%BD%8D%200100%20%3D%3E%200010.md)|简单|二进制|JS 中接收二进制参数会直接转成十进制|
|[12/13.罗马数字](./LeetCode%E9%A2%98%E8%A7%A3//12.%20%E6%95%B4%E6%95%B0%E8%BD%AC%E7%BD%97%E9%A9%AC%E6%95%B0%E5%AD%97.md)|简单|罗马数字与整数的转换|重点|

## 链表

| 题目                                           | 难度 | 类型         | 标注                 |
| :--------------------------------------------- | :--- | :----------- | :------------------- |
| [148.排序链表](./LeetCode题解/148.排序链表.md) | 普通 | 单链表排序题 | 需熟悉链表的基本操作 |

### 单链表前插技巧

可通过交换 data 的方式将前插转变为后插

```javascript
// 将s插入到p前面
s.next = p.next;
p.next = s;
const temp = p.data;
p.data = s.data;
s.data = temp;
```

### 单链表删除结点技巧

适用于不方便寻找父节点的情况  
将后继结点的值赋值到 p 上 接着删除后结点  
**力扣: 237 删除链表中的节点 只给需删除结点不给头结点就是这种情况**

```javascript
p.data = p.next.data;
p.next = p.next.next;
```

## 数组

| 题目                                                                                                                                                                     | 难度 | 类型             | 标注            |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--- | :--------------- | :-------------- |
| [658.找到 K 个最接近的元素](./LeetCode%E9%A2%98%E8%A7%A3/658.%E6%89%BE%E5%88%B0K%E4%B8%AA%E6%9C%80%E6%8E%A5%E8%BF%91%E7%9A%84%E5%85%83%E7%B4%A0.md)                      | 普通 | 数组排序题       |                 |
| [41.找出缺失的第一个正数](./LeetCode%E9%A2%98%E8%A7%A3/41.%E6%89%BE%E5%87%BA%E7%BC%BA%E5%A4%B1%E7%9A%84%E7%AC%AC%E4%B8%80%E4%B8%AA%E6%AD%A3%E6%95%B0.md)                 | 困难 | 数组区间题       |                 |
| [1331. 数组序号转换](./LeetCode%E9%A2%98%E8%A7%A3/1331.%E6%95%B0%E7%BB%84%E5%BA%8F%E5%8F%B7%E8%BD%AC%E6%8D%A2.md)                                                        | 普通 | 数组去重、排序题 | 善用 Map 和 Set |
| [1403. 非递增顺序的最小子序列](./LeetCode%E9%A2%98%E8%A7%A3/1403.%E9%9D%9E%E9%80%92%E5%A2%9E%E9%A1%BA%E5%BA%8F%E7%9A%84%E6%9C%80%E5%B0%8F%E5%AD%90%E5%BA%8F%E5%88%97.md) | 简单 | 数组排序 求和    |                 |
| [636. 函数的独占时间](./LeetCode%E9%A2%98%E8%A7%A3/636.%20%E5%87%BD%E6%95%B0%E7%9A%84%E7%8B%AC%E5%8D%A0%E6%97%B6%E9%97%B4.md)                                            | 普通 | 甘特图           |                 |
| [1105. 航班预定计划](./LeetCode题解/1105.航班预订统计.md)                                                                                                                | 普通 | 差分数组         |                 |
| [栈和队列相互转换](./LeetCode题解/栈和队列相互转换.md)                                                                                                                   | 普通 |                  |                 |

## 字符串

| 题目                                                                                                                                                       | 难度 | 类型                | 标注                       |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------- | :--- | :------------------ | :------------------------- |
| [451.根据字符出现频率排序](./LeetCode%E9%A2%98%E8%A7%A3/451.%E6%A0%B9%E6%8D%AE%E5%AD%97%E7%AC%A6%E5%87%BA%E7%8E%B0%E9%A2%91%E7%8E%87%E6%8E%92%E5%BA%8F.md) | 简单 | 字符串              |                            |
| [899. 有序队列](./LeetCode%E9%A2%98%E8%A7%A3/899.%E6%9C%89%E5%BA%8F%E9%98%9F%E5%88%97.md)                                                                  | 困难 | 字符串排序 字典顺序 | 字符串之间可以直接比较大小 |
| [640. 求解方程](./LeetCode%E9%A2%98%E8%A7%A3/640.%20%E6%B1%82%E8%A7%A3%E6%96%B9%E7%A8%8B.md)                                                               | 普通 | 字符串              | 重点                       |
| [5. 最长回文子串](./LeetCode%E9%A2%98%E8%A7%A3/5.%20%E6%9C%80%E9%95%BF%E5%9B%9E%E6%96%87%E5%AD%90%E4%B8%B2.md)                                             | 普通 | 字符串+动态规划     | 重点                       |
| [6. Z 字形变换](./LeetCode%E9%A2%98%E8%A7%A3/6.Z%20%E5%AD%97%E5%BD%A2%E5%8F%98%E6%8D%A2.md)                                                                | 普通 | 字符串遍历          |                            |
| [76. 最小覆盖子串](./LeetCode题解/76.最小覆盖子串.md)                                                                                                      | 困难 | 滑动窗口            |                            |

```javascript
/**
 * 截取字符串
 * @params {indexStart} 返回子字符串中第一个要包含的字符的索引。从0开始
 * @params {indexEnd} 返回子字符串中第一个要排除的字符的索引。从0开始
 */
substring(indexStart, indexEnd);
```

**找回文串的难点在于，回文串的的长度可能是奇数也可能是偶数，解决该问题的核心是从中心向两端扩散的双指针技巧。如果回文串的长度为奇数，则它有一个中心字符；如果回文串的长度为偶数，则可以认为它有两个中心字符。**

## 二叉树/图

| 题目                                                                                                                                                                                                         | 难度 | 类型        | 标注 |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--- | :---------- | :--- |
| [1302. 层数最深叶子节点的和](./LeetCode%E9%A2%98%E8%A7%A3/1302.%E5%B1%82%E6%95%B0%E6%9C%80%E6%B7%B1%E5%8F%B6%E5%AD%90%E8%8A%82%E7%82%B9%E7%9A%84%E5%92%8C.md)                                                | 普通 | 二叉树+回溯 |      |
| [ 623. 在二叉树中增加一行](./LeetCode%E9%A2%98%E8%A7%A3/623.%E5%9C%A8%E4%BA%8C%E5%8F%89%E6%A0%91%E4%B8%AD%E5%A2%9E%E5%8A%A0%E4%B8%80%E8%A1%8C.md)                                                            | 普通 | 二叉树+回溯 |      |
| [6267. 添加边使所有节点度数都为偶数](./LeetCode%E9%A2%98%E8%A7%A3/6267.%20%E6%B7%BB%E5%8A%A0%E8%BE%B9%E4%BD%BF%E6%89%80%E6%9C%89%E8%8A%82%E7%82%B9%E5%BA%A6%E6%95%B0%E9%83%BD%E4%B8%BA%E5%81%B6%E6%95%B0.md) | 困难 | 无向图      |      |
| [根据遍历序列构造二叉树](./LeetCode题解/二叉树构造.md)                                                                                                                                                       | 普通 | 二叉树构造  |      |

## 回溯

| 题目                                                                                                                                                                                                     | 难度 | 类型      | 标注                                         |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--- | :-------- | :------------------------------------------- |
| [46.全排列](./LeetCode%E9%A2%98%E8%A7%A3/46.%E5%85%A8%E6%8E%92%E5%88%97.md)                                                                                                                              | 普通 | 回溯      | 重点，回溯算法的基础                         |
| [79.单词搜索](./LeetCode%E9%A2%98%E8%A7%A3/79.%E5%8D%95%E8%AF%8D%E6%90%9C%E7%B4%A2.md)                                                                                                                   | 普通 | 回溯      |                                              |
| [93.复原 IP 地址](./LeetCode%E9%A2%98%E8%A7%A3/93.%E5%A4%8D%E5%8E%9FIP%E5%9C%B0%E5%9D%80.md)                                                                                                             | 普通 | 回溯      |                                              |
| [130.背围绕的区域](./LeetCode%E9%A2%98%E8%A7%A3/130.%E8%A2%AB%E5%9B%B4%E7%BB%95%E7%9A%84%E5%8C%BA%E5%9F%9F.md)                                                                                           | 普通 | 回溯      |                                              |
| [870.优势洗牌](./LeetCode%E9%A2%98%E8%A7%A3/870.%E4%BC%98%E5%8A%BF%E6%B4%97%E7%89%8C.md)                                                                                                                 | 普通 | 回溯      |                                              |
| [1863.找出所有子集的异或总和再求和](./LeetCode%E9%A2%98%E8%A7%A3/1863.%E6%89%BE%E5%87%BA%E6%89%80%E6%9C%89%E5%AD%90%E9%9B%86%E7%9A%84%E5%BC%82%E6%88%96%E6%80%BB%E5%92%8C%E5%86%8D%E6%B1%82%E5%92%8C.md) | 普通 | 回溯+异或 |                                              |
| [51.N 皇后](./LeetCode%E9%A2%98%E8%A7%A3/51.N%E7%9A%87%E5%90%8E.md)                                                                                                                                      | 困难 | 回溯      | 重点，理解后不难，经典的回溯题，面试可能会考 |
| [37.解数独](./LeetCode%E9%A2%98%E8%A7%A3/37.%E8%A7%A3%E6%95%B0%E7%8B%AC.md)                                                                                                                              | 困难 | 回溯      | 二维回溯题，区别于常规一维回溯题，需要留意   |

## 动态规划

### 解题步骤

动态规划题不同于回溯题，更多的是要找到递推公式，不需要一上来就跟回溯题一样画出遍历二叉树，可以将初始条件一条一条列出来，再慢慢推出他们之间的关系，确定好递推公式后题目就迎刃而解了。  
1.确定`dp[i]`的含义  
2.确定递推公式  
3.`dp数组`如何初始化  
4.确定遍历顺序  
5.打印`dp数组`排查 BUG

**做动态规划的题目，最好的过程就是自己在纸上举一个例子把对应的 dp 数组的数值推导一下，然后在动手写代码！**

技巧：  
求目标最值时往往用的公式是`Math.max/min(dp[j], dp[j - nums[i]] + nums[i])`；  
求排列组合时往往为`dp[j] += dp[j - nums[i]]`；

组合不强调顺序，(1,5)和(5,1)是同一个组合；  
排列强调顺序，(1,5)和(5,1)是两个不同的排列。
| 类型 | 遍历顺序 |
| :--------------: | :-------------------- |
| 01 背包 | 外`物品`升序 内`背包`降序 |
| 完全背包 | 外`物品`升序 内`背包`升序 |
| 组合问题（不强调顺序） | 外`物品`升序 内`背包`升序 |
| 排列问题 (强调顺序) | 外`背包`升序 内`物品`升序 |

| 类型                             | 递推公式                                         | 题目        |
| :------------------------------- | :----------------------------------------------- | :---------- |
| 问能否装满背包（或者最多装多少） | dp[j] = max(dp[j], dp[j - nums[i]] + nums[i])    | 416,1049    |
| 问装满背包有几种方法             | dp[j] += dp[j - nums[i]]                         | 494,518,377 |
| 问背包装满最大(最小)价值         | dp[j] = max(dp[j], dp[j - weight[i]] + value[i]) | 474         |
| 问装满背包所有物品的最小个数     | dp[j] = min(dp[j - coins[i]] + 1, dp[j])         | 322,279     |

**https://programmercarl.com/%E8%83%8C%E5%8C%85%E6%80%BB%E7%BB%93%E7%AF%87.html#%E8%83%8C%E5%8C%85%E9%80%92%E6%8E%A8%E5%85%AC%E5%BC%8F**  
**https://leetcode.cn/problems/combination-sum-iv/solutions/124393/xi-wang-yong-yi-chong-gui-lu-gao-ding-bei-bao-wen-/**

**背包题往往难在区分为背包题，并且将题目条件转换为背包题目的条件，善于挖掘题目的隐含条件。**

#### 一维数组

01 背包：外物品升序，内背包降序；  
完全背包：内外顺序随意，背包升序。

| 题目                                                                                                               | 难度 | 类型           | 标注                                         |
| :----------------------------------------------------------------------------------------------------------------- | :--- | :------------- | :------------------------------------------- |
| [62. 不同路径](./LeetCode%E9%A2%98%E8%A7%A3/62.%20%E4%B8%8D%E5%90%8C%E8%B7%AF%E5%BE%84.md)                         | 普通 | 二维 DP        | 记忆从零到一的分析过程                       |
| [343. 整数拆分](./LeetCode%E9%A2%98%E8%A7%A3/343.%20%E6%95%B4%E6%95%B0%E6%8B%86%E5%88%86.md)                       | 普通 | 一维 DP        |                                              |
| [96.不同的二叉搜索树](./LeetCode题解/96.不同的二叉搜索树.md)                                                       | 普通 | 二叉树+DP      | 二叉树的题目与传统的有点差异，要熟悉分析步骤 |
| [01 背包](./LeetCode%E9%A2%98%E8%A7%A3/01%E8%83%8C%E5%8C%85.md)                                                    | 普通 | 01 背包问题    | 重点 01 背包题型的基础 \*                    |
| [416. 分割等和子集](./LeetCode%E9%A2%98%E8%A7%A3/416.%20%E5%88%86%E5%89%B2%E7%AD%89%E5%92%8C%E5%AD%90%E9%9B%86.md) | 普通 | 01 背包问题    | 先分析题目，看能否用 01 背包                 |
| [494.目标和](./LeetCode题解/494.目标和.md)                                                                         | 普通 | 01 背包 求组合 | 区分 01 背包不同题型的模板                   |
| [474.一和零](./LeetCode题解/474.一和零.md)                                                                         | 普通 | 01 背包        | 变种的求极值 三层循环                        |
| [139.单词拆分](./LeetCode%E9%A2%98%E8%A7%A3/139.%E5%8D%95%E8%AF%8D%E6%8B%86%E5%88%86.md)                           | 普通 | 完全背包       | 完全背包 求排列                              |

#### 多重背包

多重背包：即多件物品，每个物品数量、价格都不一样，并且可能有多件，但每次只能选一件。  
思路：将多重背包展开，转换为 01 背包。  
**https://programmercarl.com/%E8%83%8C%E5%8C%85%E9%97%AE%E9%A2%98%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80%E5%A4%9A%E9%87%8D%E8%83%8C%E5%8C%85.html#%E5%A4%9A%E9%87%8D%E8%83%8C%E5%8C%85**
