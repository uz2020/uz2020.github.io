---
layout: post
title: transaction
---

事务分几种？

1. 扁平事务
2. 带有保存点的扁平事务
3. 嵌套事务
4. 分布式事务

扁平事务：所有事务都处于同一层次。
1. begin
2. commit/rollback

扁平事务的三种情况
1. 成功
2. 应用程序要求停止事务
3. 强制终止事务

扁平事务的限制: 不能提交或者回滚事务的某一部分，或分几个步骤提交