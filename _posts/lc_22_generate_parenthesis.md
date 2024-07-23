---
title: <数据结构/算法> leetcode hot100系列. 22.括号生成Generate Parenthesis
tags: [algorithm, backtrack]
categories: [Algorithm, C++, job]
date: [2024-6-15 17:50:00]
index_img: /img/algorithm.png
---

# [LeetCode hot 100] 22. 括号生成Generate Parenthesis

这个也是一道回溯题，核心是要意识到一个现有的序列中左括号数量一定不能比右括号少，否则就会出现有右括号没有左括号对应的情况。什么都没有的情况下，第一个只能来左括号，当有一个"("，第二个就可以是"("或")"。如果第二个是"("，第三个是啥都行，但是如果第二个是")"，前面的左括号和右括号就全部配对了，第三个就只能是左括号。有了这个思路，每次向下搜索时：

- 如果之前序列左括号总数大于右括号总数，下一个就可以是左括号也可以是右括号。
- 如果左括号总数等于右括号总数，下一个就只能是左括号。

那么就可以写代码了：

```c++
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        string cur;
        backtrack(ans, cur, n, n);
        return ans;
    }
    
    void backtrack(vector<string>& ans, string cur, int left, int right){
        if(left == 0){
            while(right > 0){
                cur += ")";
                right--;
            }
            ans.push_back(cur);
            return;
        }
        
        if(left < right){
            backtrack(ans, cur+"(", left-1, right);
            backtrack(ans, cur+")", left, right-1);
        }
        else{
            backtrack(ans, cur+"(", left-1, right);
        }
    }
};
```

