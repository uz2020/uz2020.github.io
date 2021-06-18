---
layout: post
title:  "Jekyll的一个Bug"
---

现在是6月17凌晨，我已经写了一篇最新的博客。但是Jekyll并没有给我编译。
也许它还活在昨天吧，因为我是在docker里编译的，所以我也不知道里面的环境
究竟是多少。但我再尝试本地编译却没有问题，因为本地认为该文件的时间是对
的。

同理，我提交的这篇博客在github的服务器上也没有被编译，估计他们那边的服
务器时间还是在6月16日吧？

Jekyll编译文件的逻辑是，它会去看文件名里包含的时间，而不是文件创建或修
改的时间。如果我想写一篇博客但不想马上公布出去，那我可以通过名字来控制
这篇博客发布的日期。所以docker和服务器都认为我希望明天发布这篇博客。实
际上由于时差，中国早已进入了他们的明天。这个其实也算不上是Jekyll的bug
了，只是一下子没这么快反应过来。如果是不搞技术的人，他们很大概率会认为
Jekyll出bug了。

为了我现在这篇博客不被埋没，我特意把时间设置在了6月16日。而我刚刚写好
的那篇，只有等明天才能发布了。