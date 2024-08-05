---
title: <数据结构/算法> leetcode hot100系列. 146. LRU缓存
categories: [Algorithm, C++, job]
date: [2024-7-28 17:50:00]
index_img: /img/algorithm.png



---

# [LeetCode hot 100] 146. LRU缓存

[题目链接](https://leetcode.cn/problems/lru-cache/description/?envType=study-plan-v2&envId=top-100-liked)

也是高频题目了，为了使查询get达到O(1)，自然是用哈希表来存储，但是又需要在O(1)时间完成删除（即逐出）、调整顺序等操作。这里使用**双向链表**比较合适，主要是因为比较方便删除。传统的单向链表想要删除还需要再遍历一遍获取前驱节点，而双向链表自带前驱节点，可以不用遍历就直接删除。

将二者结合，则哈希表应该保存<key, 链表节点>这样的键值对，这样可以通过key直接找到对应的节点，方便移位。

另外为了实现放到头部和删除尾部元素这两个操作，需要建立头结点和尾节点，注意不是头指针和尾指针，而是单独的头节点和尾节点。

完整代码如下

```cpp
struct BiListNode{
    int key;
    int value;
    BiListNode* prev;
    BiListNode* next;
    BiListNode(int key, int value):key(key), value(value){}
};
class LRUCache {
private:
    BiListNode* head;
    BiListNode* tail;
    int capacity;
    int usage;
    unordered_map<int, BiListNode*> keyMap;
public:
    LRUCache(int capacity) {
        this->head = new BiListNode(0, 0);
        this->tail = new BiListNode(0, 0);
        head->next = tail;
        tail->prev = head;
        head->prev = tail->next = nullptr;
        this->capacity = capacity;
        this->usage = 0;
    }
    
    int get(int key) {
        if(!keyMap.count(key)) return -1;
        BiListNode* entry = keyMap[key];
        //从原本位置拿走
        entry->prev->next = entry->next;
        entry->next->prev = entry->prev;    
        head->next->prev = entry;
        //放到头部
        entry->next = head->next;
        head->next = entry;
        entry->prev = head;
        return entry->value;
    }
    
    void put(int key, int value) {
        if(!keyMap.count(key)){
            if(usage >= capacity){
                //删除尾部元素，注意在哈希表里也要删除
                BiListNode* tmp = tail->prev;
                keyMap.erase(tmp->key);
                tail->prev->prev->next = tail;
                tail->prev = tmp->prev;
                delete tmp;
                usage--;

            }
            //在头部插入新元素
            BiListNode* entry = new BiListNode(key, value);
            entry->next = head->next;
            entry->prev = head;
            entry->next->prev = entry;
            head->next = entry;
            keyMap.emplace(key, entry);
            usage++;

        } else{
            BiListNode* entry = keyMap[key];
            entry->value = value; //更新value值
            //放到头部
            entry->prev->next = entry->next;
            entry->next->prev = entry->prev;
            entry->next = head->next;
            head->next->prev = entry;
            head->next = entry;
            entry->prev = head;
        }
    }
};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```

