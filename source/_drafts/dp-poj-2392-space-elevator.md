title: POJ 2392 Space Elevator
tags: 
- backpack
- DP
categories:
- algorithm
- POJ
---
# 题目分析
可以先根据block的最大海拔高度来排序，从低到高，
bool dp[i][j] 代表从小于等于标号i的block中，是否能够得到的最大高度j 
if(j <= ai)
    dp[i][j] = dp[i-1][j] || dp[i-1][j-hi] || dp[i-1][j-hi*2] || ....
else 
    dp[i][j] = dp[i-1][j]
j的上限为最大的ai
复杂度 O(n * max(ai) * max(ci))  10 * 400 * 40000  = 16 * 10^7  fine~

初始化 dp[0][0] = true
每一步始终记录max(j) 即为最终结果。

2184 Cow Exhibition
TS之和为正。 TF之和为正，并且最后的TS+TF为最大

两个都为正的个体一定加入到其中，
两个之和为负的个体一定要去掉吗？是的！
一正一负搭配起来
有点类似背包，哈哈哈，多了一些条件！

