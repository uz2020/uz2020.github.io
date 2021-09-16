---
layout: post
title: ubuntu启动慢的可能原因
---

```
Gave up waiting for resume from suspend.
```

今天的ubuntu出现这种情况，以至于启动接近一分钟才进入系统，后来在kernel cmd补充resume的配置就可以了。

今天给ubuntu扩容，为了编译mysql最新版。mysql最新版的编译工作量真的大，生成的文件也多，规模应该在15G左右。

要编译很多的c++代码，只好用了一个老旧的台式机来编译（至少比我的笔记本优秀一些）。i3-2100，这应该差不多是十年前的cpu了，主频3.1GHz，双核四线程。我想之后有必要的话换上一代神u（至强e3 1230 v2）试试。
