---
layout: post
title:  "使用avd帮助dwm显示系统负载"
---

这个工具的地址是：[avd](https://gitlab.com/narvin/avd)

最简单的用法是在.xinitrc里添加以下两行代码：

```bash
avdd &
avds &
```
这样dwm右上角就会显示时间、音量、电池等信息。但没看到温度和风扇转速的信息。有空再研究一下。

效果：

![](/assets/img/avd.png)
