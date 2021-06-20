---
layout: post
title:  "Work Harder"
---

晚上两点，总算把项目的主要流程调通，明天再写点业务逻辑就好。

从昨晚了解了nsq，到今天把它应用起来，效果还算不错，把该解耦的部分解耦出来了，逻辑更加清晰。

把nsq安装在了docker里。启动没有问题，请求就出问题了。首先docker-compose.yml有地方是遗漏的。比如端口的转换和nsqd没有设置broadcast ip。这就导致外部的client通过nsqlookupd请求nsqd的服务时，client并不能正确地访问nsqlookupd返回的remote_address。

stackoverflow上看到一位中国朋友问了这个问题，好奇之下去看看这位朋友的博客。比我的有深度多了。而且他已经写了接近7年，可以看出在很用心地写。

他从2014年接触go，一直在ruby社区活跃，在stackoverflow上也解答了一些问题。而关于nsq，他则是2018年接触的。虽然不知道他多大年龄，但他真是值得学习的榜样。我看到了我们之间的差距，更不敢对自己的事业懈怠。
