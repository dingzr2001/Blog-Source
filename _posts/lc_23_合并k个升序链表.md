---
title: <数据结构/算法> leetcode hot100系列. 23. 合并k个升序链表
categories: [Algorithm, C++, job]
date: [2024-8-20 17:50:00]
index_img: /img/algorithm.png
---

# [LeetCode hot 100] 23. 合并k个升序链表

[题目链接](https://leetcode.cn/problems/merge-k-sorted-lists/description/?envType=study-plan-v2&envId=top-100-liked)

如果是合并两个链表那没得说，两个指针分别指向两个链表，每次把小的那个后移。

但是这里是k个，如果用k个指针，每次移动最小的，那么每次都需要遍历一遍找到最小的那个，时间复杂度太高了。

从k个里面找最小，这个情景很适合使用【堆】这种结构。关于堆的知识点[传送门](./我一定要学会[堆].md)

这里使用小根堆，因为每次要获得最小的，那么小根堆取堆顶就能很轻松地完成这个工作。堆顶是一个指针，指向某个链表的遍历位置，当取了堆顶之后，需要将遍历指针向后移动一位，并且重新加入到堆中，这里就是把堆顶替换掉，然后**向下调整**，最为方便。

下面是完整代码，其中我偷了点懒，建堆就直接用排序代替了。

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    void adjustDown(vector<ListNode*>& heap){
        int parent = 0;
        while(2 * parent + 1 < heap.size()){
            int lchild = 2 * parent + 1;
            int minChild = lchild;
            if(lchild + 1 < heap.size()){
                int rchild = lchild + 1;
                minChild = heap[lchild]->val < heap[rchild]->val ? lchild : rchild;
            }
            if(heap[parent]->val < heap[minChild]->val){
                return;
            }
            swap(heap[parent], heap[minChild]);
            parent = minChild;
        }
        return;
    }
    void headPop(vector<ListNode*>& heap){
        swap(heap[0], heap[heap.size() - 1]);
        heap.pop_back();
        adjustDown(heap);
    }

    ListNode* mergeKLists(vector<ListNode*>& lists) {
        vector<ListNode*> heap;
        for(auto& head : lists){
            if(head) heap.push_back(head);
        }
        //这里用排序实现了建堆，效果是一样的
        sort(heap.begin(), heap.end(), [](ListNode* a, ListNode* b){return a->val < b->val;});
        ListNode dummy(0);
        ListNode* cur = &dummy;
        while(heap.size() > 0){
            cur->next = new ListNode(heap[0]->val);
            cur = cur->next;
            if(heap[0]->next == nullptr){
                headPop(heap);
            } else {
                heap[0] = heap[0]->next;
                adjustDown(heap);
            }
            cout<<heap.size()<<endl;
        }
        return dummy.next;
    }
};
```

自己独立写出来了，有点子感动。