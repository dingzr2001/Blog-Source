---
title: C++ STL常用容器及使用方法
tags: [C++, STL]
categories: [Programming Language, C++]
date: [2024-3-23 23:46:00]
index_img: /img/STL.jpg
---



# C++的STL库常用数据结构及方法

## 1. vector可变数组

### 1.1 引入

```cpp
#include<vector>
```

### 1.2 创建

- 创建空vector

```cpp
vector<int> vct;
```

- 创建一定长度的vector

```cpp
vector<int> vct(10);
```

- 创建一定长度的vector，并且给所有元素赋初始值

```cpp
vector<int> vct(10, 1);
```

- 创建一个指定元素值的vector

```cpp
vector<int> vct{1, 2, 3, 4, 5};
```

- 使用迭代器创建vector

```cpp
vector<int> tmp{1, 2, 3, 4, 5};
vector<int> vct(tmp.begin(), tmp.end());
```

### 1.3 方法

- 向尾部添加元素push_back, emplace_back

  push_back()会先创建一个临时对象，然后复制或移动到容器尾部。emplace_back是C++ 11新引入的方法，直接在容器尾部创建这个对象。

  ```cpp
  vct.push_back(1);
  vct.emplace_back(1);
  ```

- 向某一位置插入元素insert，emplace

  同理，emplace是C++ 11新特性

  ```cpp
  vct.insert(pos, val);//在pos位置插入元素val
  vct.insert(vct.begin()+2, 1);//在下标为2处插入1
  vct.insert(vct.begin()+2, 3, 1);//在下标为2处插入3个1
  ```

- 删除尾部元素pop_back

  ```cpp
  vct.pop_back();
  ```

- 删除指定位置的元素erase

  ```cpp
  vct.erase(vct.begin()+2);//删除下标为2的元素
  vct.erase(vct.begin()+2, vct.begin()+5);//删除下标为2到4的元素
  ```

- 清空vector

  ```
  vct.clear();
  ```

## 2. stack栈

### 2.1 引入

```<stack
#include<stack>
```

### 2.2 构造stack对象

```cpp
stack<int> st;
```

### 2.3 方法

- 入栈push

  ```cpp
  st.push(1);
  st.emplace(1);
  ```

- 出栈pop

  ```cpp
  st.pop();//void函数，仅出栈操作
  ```

- 获取栈顶top

  ```cpp
  int a = st.top();
  ```

- 判空empty

  ```cpp
  if(!st.empty()){
      ...
  }
  ```

## 3. 队列queue

### 3.1 引入

```cpp
#include<queue>
```

### 3.2 构造queue对象

```cpp
queue<int> q;
```

### 3.3 方法

- 入队push，emplace

  ```
  q.push(1);
  q.emplace(1);
  ```

- 出队pop

  ```cpp
  q.pop();
  ```

- 获取队头、队尾元素front，back

  ```cpp
  int a = q.front();
  a = q.back();
  ```

## 4. 双向队列deque

### 4.1 引入

```cpp
#include<deque>
```

### 4.2 构造deque对象

```cpp
deque<int> dq;
deque<int> dq(len, val);
```

### 4.3 方法

- 前端插入元素push_front，emplace_front

  ```cpp
  dq.push_front(1);
  dq.emplace_front(1);
  ```

- 后端插入元素push_back，emplace_back

  ```cpp
  dq.push_back(1);
  dq.emplace_back(1);
  ```

- 前后端出队pop_front，pop_back

  ```cpp
  dq.pop_front();
  dq.pop_back();
  ```

- 访问元素front，back

  ```cpp
  int a = dq.front();
  int b = dq.back();
  ```

- 清空clear

  ```cpp
  dq.clear();
  ```

## 5. 集合set

### 5.1 引入

```cpp
#include<set>
```

### 5.2 构造set

```cpp
set<int> s;
```

### 5.3 方法

- 插入元素insert，emplace

  ```cpp
  s.insert(1);
  s.emplace(1);
  ```

- 删除元素erase

  ```cpp
  s.erase(1);//删除值为1的元素
  ```

- 查找元素count，find

  ```cpp
  if(s.count(1)){//若存在则返回1，否则返回0
      ...
  }
  if(s.find(1) != s.end()){//若存在则返回迭代器，否则返回end()
      ...
  }
  ```

- 清空clear

  ```cpp
  s.clear();
  ```

## 6. 键值映射map

### 6.1 引入

```cpp
#include<map>
```

### 6.2 构造

```cpp
map<char, int> mp;
```

### 6.3 方法

- 插入元素insert, emplace

  ```cpp
  mp.insert(pair<char, int>('a', 1));
  mp.emplace('a', 1);
  ```

  

