---
title: <数据结构/算法> leetcode hot100系列. 46.全排列Permutation
tags: [algorithm, backtrack]
categories: [Algorithm, C++, job]
date: [2024-6-15 17:50:00]
index_img: /img/algorithm.png
---

# [LeetCode hot 100] 46. 全排列Permutation

拿到这道题的时候总感觉答案就在嘴边，但是写了写就感觉怎么都不对劲，原因是没有意识到这是一个回溯题。所谓回溯算法，百度百科给出的定义是：

> 回溯法也称试探法，它的基本思想是：从问题的某一种状态（初始状态）出发，搜索从这种状态出发所能达到的所有“状态”，当一条路走到“尽头”的时候（不能再前进），再后退一步或若干步，从另一种可能“状态”出发，继续搜索，直到所有的“路径”（状态）都试探过。这种不断“前进”、不断“回溯”寻找解的方法，就称作“回溯法”。

回溯算法最大的特征就是它是自顶向下的，也就是从顶部的什么都没有，慢慢往深处探索，在最底端得到答案。

对于这道题而言，一开始是什么都不知道的，假如说有个序列[1, 2, 3]，首先确定出第一个数，可以是[1]，可以是[2]，可以是[3]。当第一个数为1时，再确定第二个数，可以是[1, 2]或[1, 3]，然后确定第三个数。当我们确定第三个数之后的时候，实际上是已经得到了两个答案，就应该把这两个答案立即加到答案集合中。也就是说，我们写出来虽然也是递归的形势，但是只是用递归实现了类似栈的回退作用，答案并不是在最顶层得到，而是在最底层得到。

具体如何操作请见代码。

```c++
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> cur;
        int* visited = new int[nums.size()]{0};
        backtrack(nums, cur, visited, ans);
        return ans;
    }

    void backtrack(vector<int>& nums, vector<int> cur, int* visited, vector<vector<int>>& ans){
        if(cur.size() == nums.size()) {
            //探到最底层，得到一个答案，放到结果中
            ans.push_back(cur);
            return;
        }
        for(int i = 0; i < nums.size(); i++){
            if(visited[i] == 0){
                //往下探索一层
                cur.push_back(nums[i]);
                visited[i] = 1;
                backtrack(nums, cur, visited, ans);
                //回到当前这一层
                visited[i] = 0;
                cur.pop_back();
                
            }
        }
    }
};
```

