---
title: Python Interview-1. Variables and Copy
tags: [python, variable, copy]
categories: [python, basics]
date: [2023-02-25 20:51:00]
index_img: /img/python.jpg
---

# [Python Interview]1. Variables and Copy

## Variable Types

### immutable variable

immutable variables include :

- int
- string
- tuple

When a function is called, the immutable variable parameters are passed by value.

For immutable variables, shallow copy and deep copy both will not allocate new memory space for the new variable. The two kinds of copies only build a reference relationship between the old object and the new object.

### mutable variable

immutable variables include:

- set
- list
- dict

When a function is called, the mutable variable parameters are passed by reference.

For mutable variables, shallow copy and deep copy both will allocate new memory space for the new variable. However, for the immutable variables contained in mutable variables like lists, they are still shallow copied. So the deep copy of mutable variables just copy themselves, excluding the immutable variables contained.

## two kinds of 'copy'

Shallow copy is quite unanimous for all kinds of variables. However, deep copy for complex variables that have multi layers is worth discussing.

Actually, the principle can be concluded as:

- For each layer, follow the following rules recursively:
- If the current layer is immutable, and all sub-elements are immutable, then no new space and object will be allocated for this layer.
- If the current layer is immutable, and some of the sub-elements are mutable, then deep-copy these sub-elements and the current layer.
- If the current layer is mutable, create a new object for the current layer, and copy the sub-elements depending on their mutability recursively.

That's based on my comprehension, some might be wrong.

## References

https://zhuanlan.zhihu.com/p/487677774

https://mp.weixin.qq.com/s?__biz=MzU0NjgzMDIxMQ==&mid=2247569389&idx=4&sn=7f93a257c597821bb55da3e2637a3f3d&chksm=fb542d01cc23a4178b83617a01e933494ca6274dd2086baf2bd0354ac30637cc9af674458116&scene=27

https://blog.csdn.net/m0_59044674/article/details/127267173

https://towardsdatascience.com/an-overview-of-mutability-in-python-objects-8efce55fd08f