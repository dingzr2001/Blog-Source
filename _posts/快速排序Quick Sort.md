---
title: <数据结构/算法> 1.快速排序Quick Sort
tags: [sort, algorithm]
categories: [Algorithm, C++, job]
date: [2024-6-11 23:46:00]
index_img: /img/algorithm.png
---

# 快速排序Quick Sort

事实上对于快速排序一直以来都是懵懵的，苦想后终于茅塞顿开。

首先，快速排序是一个递归的算法，核心思想是在一个序列中选择一个分界值（Pivot），将该序列中的元素划分为左右两部分，左边的元素均小于分界值，右边的元素大于分界值。然后对左右两边分别递归地执行此操作。

那么怎样执行这个“分成左右两边”的操作呢？目前最简洁的方法是这样的。

- 设立一个快指针fast，一个慢指针slow。快指针用于循环，慢指针用于指向一个位置。
- 指向的位置是什么呢？就是下一次遇到比pivot值小的元素时，应该把它填在哪里。
- 那么这个位置怎么确定呢，很简单，从序列最左边开始，每找到一个比pivot值小的元素就放到左边，然后往右挪一下就行了。对应到slow指针也就是每次往slow指针位置放，然后slow++。
- 那么原来位置上的元素怎么办呢？直接和当前这个快指针指向的元素，也就是那个比pivot值小的元素，互换位置就好了。
- 这样做，遍历一遍后，比pivot小的值都往左边不断地堆放，最后全在左边了。剩下的比pivot大的值自然就跑到右边去了。
- 但是还有一个pivot值没有处理，这个值往往是选择序列最左边或者最右边的元素，我们在刚刚的遍历中直接略过。也就是如果选了最左边的元素，一开始遍历就直接从第二个元素开始了。如果是最右边的元素，遍历的时候就到倒数第二个元素停止。
- 最后剩下的slow元素，很明显是要放到中间的。注意到这个时候slow指针正好就在中间，交换位置即可。这里有两种情况：
  - 一种是每次交换后slow再++，也就是下一次放就是直接放到slow的位置。这种情况下遍历结束后的slow应该是指向第一个比pivot大的元素，也就是右边序列的最左端。如果pivot是左端点则需要和slow-1位置互换，这样把slow-1也就是最后一个比pivot小的值换到最前面，如果pivot是右端点则直接和slow互换，这样把第一个比pivot大的值换到最后面。
  - 第二种是先slow++再交换，这样每一次交换前需要先把slow++挪到下一个位置。这种情况下遍历结束后的slow就是最后一个比pivot小的元素。对于两种pivot位置和第一种情况同理。

以下是C++代码：

```c++
#include<bits/stdc++.h>
#include<vector>
using namespace std;

int partition(vector<int> &arr, int left, int right){
    int pivot = arr[right];
    int slow = left;
    for(int fast = left; fast < right; fast++){
        if(arr[fast] < pivot){
            int tmp = arr[slow];
            arr[slow] = arr[fast];
            arr[fast] = tmp;
            slow++;
        }
    }
    arr[right] = arr[slow];
    arr[slow] = pivot;
    return slow;
}

void quickSort(vector<int> &arr, int left, int right){
    int p = partition(arr, left, right);
    quickSort(arr, left, p-1);
    quickSort(arr, p+1, right);
}

int main(){
  vector<int> arr = {7, 9, 2, 5, 1};
  quickSort(arr, 0, arr.size() - 1);
  for (int i = 0; i < arr.size(); i++){
    cout << arr[i] << ",";
  }
}
```

其实就是这么简单的道理，不知道自己以前为什么那么久没搞明白，还是太菜了。

