---
title: <数据结构/算法> 各种背包问题详解（01背包，完全背包，多重背包）
tags: [algorithm, dp]
categories: [Algorithm, C++, job]
date: [2024-6-15 17:50:00]
index_img: /img/algorithm.png


---

# # 动态规划-各种背包问题详解

记得当时学算法的时候可喜欢写背包问题了，因为学会了有种豁然开朗的感觉，仿佛掌握了武功秘籍。这篇文章就来记录一下各种背包问题的场景和解法。

### 1. 01背包问题

01背包问题是这样的：

> 假如有一组n个物品，每个物品i都有一个体积v~i~和一个价值s~i~，现在有一个容量有限为V的背包，如何在不超过该背包容量的情况下装走总价值最大的物品？

这个问题的思想是通过从少到多地考虑装入的物品。首先将物品进行编号，然后使用一个二维的dp数组保存子问题的解。$dp[i][j]$表示考虑前i个物品，背包容量为j时所能装入的最大价值，那么问题的所求就是$dp[n][V]$

$dp[i][j]$可能包含第i个物品，也可能不包含。不包含时就是$dp[i-1][j]$，包含时就是$dp[i-1][j-v_i]+s_i$，也就是第i个物品的价值，加上去掉第i个物品占的空间后，装前i-1个物品最大能获得多少价值。因此有状态转移方程：

$dp[i][j]=max(dp[i-1][j], dp[i-1][j-v_i]+s_i)$

那么怎样动态地更新这个二维数组呢？答案是都行，即可以每次增加一个容量，也可以每次增加一个物品。这里按照每次增加一个物品来写。

```c++
#include<iostream>
#include<vector>
using namespace std;
class Solution{
public:
    int maxValue(vector<int>& volume, vector<int>& value, int V){
        vector<vector<int>> dp;
        for (int i = 0; i < volume.size() + 1; i++) {
            vector<int> row(V + 1, 0);
            dp.push_back(row);
        }
        for (int i = 0; i < volume.size() + 1; i++){
            for (int j = 0; j < V + 1; j++){
                if(i == 0 || j == 0){
                    dp[i][j] = 0;
                    continue;
                }  
                int newMax = 0;
                if(j >= volume[i-1]){
                    newMax = dp[i - 1][j - volume[i-1]] + value[i-1];
                }
                dp[i][j] = max(dp[i - 1][j], newMax);
            }
        }
        return dp[volume.size()][V];
    }
};
int main(){
    vector<int> volume{1, 2, 4, 8, 10};
    vector<int> value{2, 3, 4, 5, 10};
    int V = 20;
    Solution s;
    cout << "max value: " << s.maxValue(volume, value, V);
    return 0;
}
```

### 2. 完全背包

完全背包就是每个物品有无限个。对于$dp[i][j]$，可以是$dp[i][j-v_i]+s_i$，表示少装一个当前物品的最大价值再加上当前物品的价值；也可以是$dp[i-1][j]$，表示不装当前物品，只装前i-1个物品时的最大价值。也就是

$dp[i][j]=max(dp[i][j-v_i]+s_i,dp[i-1][j])$

代码就懒得重新写了，就把上一个的状态转移方程改一下就行。

### 背包问题优化

#### 01背包问题优化

你说这玩意谁研究的呢，太聪明了。

我们看这个一层一层的二维数组，事实上每次只有最下面那行$dp[i-1][1,2,...,V]$才有用，再更新下一行的时候只需要用到之前的最下面那行，因此就可以压缩成一维的，像刷墙一样一遍一遍地覆盖。此时我们只需要一个数组$dp[V]$，在第i次循环中，$dp[j]$就表示考虑前i个物品时，容量为j的背包最大价值。那么状态转移方程就是：

$dp[j]=max(dp[j],dp[j-v_i]+s_i)$

这里max里面的$dp[j]$就是上一轮（i-1轮）的，表示考虑前i-1个物品时容量为j的最大价值。那么这里就有一个问题。按照原来的顺序更新，应该从$dp[1]$更新到$dp[V]$，但是更新到后面，$dp[j-v_i]+s_i$又需要用前面的值，但是前面的值又已经被覆盖了，可能就已经包含了第i个物品，这里如果再拿一个，不符合01背包的定义了。因此这里我们需要**倒序**更新，这样保证前面的值还是上一轮的。

```c++
int knapsack01Optimized(vector<int>& volume, vector<int>& value, int V){
    vector<int> dp(V + 1, 0);
    for (int i = 0; i < volume.size() + 1; i++){
        for (int j = V; j >= 0; j--){
            if(j >= volume[i - 1]){
                dp[j] = max(dp[j], dp[j - volume[i - 1]] + value[i - 1]);
            } else{
                dp[j] = dp[j];
            }
        }
    }
    return dp[V];
}
```

这样的时间复杂度其实没变，空间复杂度从$O(NV)$降低为$O(V)$

#### 完全背包问题优化

01背包问题要从后往前更新，而多重背包问题恰好相反，因为人家就是要考虑算了多个当前物品的情况。区别在哪呢，我们观察完全背包问题的状态转移方程

$dp[i][j]=max(dp[i][j-v_i]+s_i,dp[i-1][j])$

可以发现这里出现是$dp[i][j-v_i]$，也就是当前这一层的前面的值，这也就说明我们需要先更新前面再更新后面。

### 多重背包

