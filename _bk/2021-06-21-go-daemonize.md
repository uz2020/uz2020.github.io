---
layout: post
title:  "Go进程后台运行"
---

当发现Go没有提供daemon相关的包时，我确实有点惊讶。作为后台服务标配的操作，竟然没有提供解决方案。难道要像apue那样去fork进程、关闭stdin、stdout？关于这种daemon的操作我记不得具体细节，只是在印象里这件事情有点麻烦。

所以一个Go进程究竟要如何后台运行？总觉得nohup不优雅。而大多数流行的后台服务都是直接后台运行的，日志输出到文件。然而今天我Google之后发现原来用systemd来做后台运行也可以。那么就更加方便了。我们不用维护日志文件。我们不用担心进程使用stdin、stdout。通通用systemd来管理就好了。至于细节，我不想展开讲。总之，操作并不复杂。

写一个myapp.service文件，放在/etc/systemd/system目录中：

```
[Unit]
Description=psfd data sync daemon

[Service]
ExecStart=/opt/psfd-sync/server
WorkingDirectory=/opt/psfd-sync
Restart=always

[Install]
WantedBy=multi-user.target
```

该文件设置好了工作目录和可执行文件的路径。也可以在里面配置环境变量。

然后用rsync同步文件：

```bash
rsync -az --delete ~/deploy/psfd-sync/ p:/opt/psfd-sync
```

最后启动服务：

```bash
systemct start psfd-sync
```

查看日志（可以同时查看多个服务的日志，follow的方式则是live-tail模式）：

```bash
journalctl  --follow  -u psfd-sync -u psfd-events
```

