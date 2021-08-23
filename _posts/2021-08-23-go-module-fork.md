---
layout: post
title: go module fork问题
---

今天在使用一个第三方go module有个bug，看到有人提交fix，但作者很久不更新了。

遇到这种情况，我想着先fork过来，改掉再说。可是fork过来的module，除了修复bug之外，import路径也变了。所以我fork过来的module不能直接用。

因而用以下命令来替换：

```bash
go mod edit -replace 
```

fork过来修复bug之后的版本要发布出去，替换时要指定版本。以上replace命令既可以把一个模块替换成本地的，也可以替换成自己fork后的项目地址(通过版本来控制)。
