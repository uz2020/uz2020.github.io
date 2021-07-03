---
layout: post
---

docker执行命令总是说我没有权限，害得我老是sudo。今天去聪爷家用他的电脑发现并不需要sudo。于是觉得奇怪。回来一查，原来我安装完docker少了一个步骤：

```bash
sudo usermod -aG docker $USER
```
