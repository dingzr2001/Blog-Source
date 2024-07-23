---
title: <数据结构/算法> 2.归并排序Merge Sort
tags: [sort, algorithm]
categories: [Algorithm, C++, job]
date: [2024-6-12 17:50:00]
index_img: /img/algorithm.png

---

# 归并排序Merge Sort

归并排序个人感觉理解起来没什么难度，主要就是一个递归地合并子序列的过程，直接写代码，在代码特殊的地方说明一下

```c++
#include<bits/stdc++.h>
#include<vector>
using namespace std;

void merge(vector<int> &arr, int left, int mid, int right){
    vector<int> tmp(right - left + 1);
    int pl = left, pr = mid;
    while(pl <= mid - 1 && pr <= right){
        if(arr[pl] < arr[pr]){
            tmp.push_back(arr[pl++]);
        }
        else{
            tmp.push_back(arr[pr++]);
        }
    }
    //下面这两个循环只会执行一个，因为不可能左右子列都有元素剩余
    while(pl <= mid - 1){
        tmp.push_back(arr[pl++]);
    }
    while(pr <= right){
        tmp.push_back(arr[pr++]);
    }
    int j = 0;
    for(int i = left; i <= right; i++){
        arr[i] = tmp[j++];
    }
}

void mergeSort(vector<int> &arr, int left, int right){
    if(left >= right) return;
    int mid = (left + right) / 2;
    mergeSort(arr, left, mid - 1);
    mergeSort(arr, mid, right);
}
```

