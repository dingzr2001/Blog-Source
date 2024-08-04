---
title: <数据结构/算法> leetcode hot100系列. 25. k个一组翻转链表
categories: [Algorithm, C++, job]
date: [2024-7-28 17:50:00]
index_img: /img/algorithm.png


---

# [LeetCode hot 100] 25. k个一组翻转链表

[题目链接](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/description/?envType=study-plan-v2&envId=top-100-liked)

好折磨的题，要想明白还真是得费一些脑筋。

我们已经做过[LeetCode 206 反转链表](https://leetcode.cn/problems/reverse-linked-list/description/?envType=study-plan-v2&envId=top-100-liked)，直到反转链表的思路是保留一个前驱节点，然后每次让后继结点的next指向前驱节点。这个题中对于每一组中的反转自然也可以这样做。

不过难点就在于局部反转后怎样和原链表接起来。思路我大致画了个图（假设k=2）：

![](D:\dingblog\source\img\leetcode\lc_25.png)

这里有个很tricky的点，就是这个prev的设置。由于我们需要确定一组k个节点中的最后一个，所以需要使用快慢指针，快指针比慢指针领先k，并且二者每轮同时后移k个。慢指针是当前k个的前一个，也就是next指向当前k个中第一个的节点，而快指针就是当前k个的最后一个。在反转整个链表时，我们首先令prev=nullptr，这是因为原先的第一个节点会变成最后一个，而最后一个节点的next指向的就是null。但是这里不同，我们每一组的第一个节点变成最后一个节点后，还需要指向下一组的头，所以prev在每组开始反转的时候都应该先指向下一组的头，那么这个下一组的头就是fast->next。

每组反转之后，这一组的尾就已经接在下一组的头上了，但是注意，每一组的头都会变的，所以在遍历之后应该让上一组的尾重新指向这个新的头。新的头就是反转结束后prev的位置，而上一组的尾就是slow，所以需要slow->next = prev;

挺绕的，还是看图好懂一点吧~

完整代码如下

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
    ListNode* reverseKGroup(ListNode* head, int k) {
        ListNode dummy(0);
        dummy.next = head;
        ListNode* fast = &dummy;
        ListNode* slow = fast;

        while(fast){
            for(int i = 0; i < k; i++){
                if(!fast->next) return dummy.next;
                fast = fast->next;
            }
            ListNode* prev = fast->next;
            ListNode* cur = slow->next;
            while(prev != fast){
                ListNode* tmp = cur->next;
                cur->next = prev;
                prev = cur;
                cout<<cur->val<<endl;
                cur = tmp;
            }
            
            ListNode* newEnd = slow->next;
            newEnd->next = cur;
            slow->next = fast;
            slow = fast = newEnd;
            
        }
        return dummy.next;
    }
};
```

