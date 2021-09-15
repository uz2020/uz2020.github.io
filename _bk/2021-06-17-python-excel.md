---
layout: post
title:  "Python读取excel导入数据库"
---

哎，目前还摆脱不了要和搞Windows的人合作开发。对方不知道用的哪个Windows
上的数据库服务器吧，一键导出到了excel表作为数据给到我们。你说几十行或
上百行的excel也就算了，搞个两万多条记录的excel表我也是有点无语了。难怪
那些用office的人动不动就说电脑卡。

But this time, Python comes to my rescue。Python有个包包叫做pandas，和
numpy是一个类型的，都是处理数据。这个包包还挺好用的，但有几个要注意的点：

1. to_sql会使用engine建表，建表如果是中文，则要使用utf8mb4编码。
2. codecs不能识别utf8mb4，因此需要注册。
```python
import codecs
codecs.register(lambda name: codecs.lookup('utf8') if name == 'utf8mb4' else None)
```
3. 除了Python方面注册编码，mysql也要设置character set。我的mysql服务安
   装在docker上，官方有讲怎么设置这个参数。（不得不说官方提供的mysql
   docker容器用起来真的很方便！）
