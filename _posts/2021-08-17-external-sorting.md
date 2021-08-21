---
layout: post
---

排序在一个数据库里的应用场景有哪些？
1. answers in some order
2. bulk loading a tree index
3. eliminating duplicate copies in a collection of records
4. join

## two-way merge sort

假设用3个buffer page的内存做external sorting。

1. pass 0: 逐个disk page加载到内存排序好，写出到disk page
2. pass 1: 两个buffer page (input)加载已经排序好的disk page，merge sort到另外一个buffer page (output)，每当填满就flush到disk。完成之后，每个
