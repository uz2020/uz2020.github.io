---
layout: post
title:  "jekyll in docker"
---

因为想编辑一个pdf所以去安装okular这样的kde大户。但我的系统有一段时间没
更新，结果安装不上。而另外的pdf工具就报动态库版本不一致。遇到这种情况
我都是直接升级系统。升级完了，ruby版本从2.7升到了3.0，然后jekyll就说它
不干了，它要在ruby2.7下面才开心。那好吧，我索性把jekyll也给升级了，结
果这家伙还是给我报错，我的环境已经不适合它生存了，虽然还能给我serve一
下，但已经无法livereload了。这样我怎么忍受得了，难不成我编辑一下md文件
之后还要去手动重启一下jekyll才能查看博客的最新模样？这样的结果我自然是
无法接受的。那好吧，jekyll你不喜欢在这里生存了，你认为你赖以生存的环境
被破坏了，那你就学习最近北上的大象群吧，反正我是不管你们了。

硬气的我虽然嘴上这么说着，却早已神不知鬼不觉悄悄地去docker hub搜索
jekyll容器了。没错，这就是我爱的容器，TA的名字就是
bretfisher/jekyll-serve。

跟着它的教程来，很快就部署好容器了，只在下载ruby gem时等待了些许时间。
当我看到命令行上我最爱的那几行日志出现时，我努力控制我那因为激动而颤抖
的手，在浏览器上快速地敲下：localhost:4000

```
Configuration file: /site/_config.yml
            Source: /site
       Destination: /site/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
       Jekyll Feed: Generating feed for posts
                    done in 0.875 seconds.
/usr/local/bundle/gems/pathutil-0.16.2/lib/pathutil.rb:502: warning: Using the last argument as keyword parameters is deprecated
 Auto-regeneration: enabled for '/site'
LiveReload address: http://127.0.0.1:35729
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

什么，竟然连接不上！心情马上变得糟糕，但我还是坚强地打开了一个终端，输
入那句我已经厌烦的命令：

```bash
telnet localhost 4000
```

结果它却给我这个：

```bash
root@worker:~# telnet localhost 4000 
Trying xx.xx.xx.xx...
Connected to spark.
Escape character is '^]'.
Connection closed by foreign host.
```

什么，居然直接给我关闭了？怎么回事，这么不给面子！

我怀着失落的心情一次又一次地问自己，到底哪里做错了。无神的眼睛盯着屏幕
发呆了多久我也不知道。当我晃过神来，我看到的是这两行东西：

```
LiveReload address: http://127.0.0.1:35729
    Server address: http://127.0.0.1:4000/
```

啊哈，maybe，也许，大概，就是你们俩在搞怪了。我知道我离真理越来越近，
越是这个时候我就越是故作镇静。因为我知道武林高手出大招之前都是一副风轻
云淡的样子的，况且酷炫如我，怎么能轻易表露出喜悦之情，这样别人只会觉得
我这个人得意忘形。

于是，我去煮了一壶开水，细致地把茶壶冲洗一下。再用镊子夹出些许乌龙茶放
进茶壶，开始砌茶。手中的茶杯已经见底，我轻轻地放下茶杯。然后就坐在电脑
桌前，只见手指轻快地敲着，屏幕刷刷地显示着在不断变化中的信息。随着一个
干净利落的回车，我的双手早已写意地从键盘收回插进了裤兜。这时，只见屏幕
已经恢复了当年的风采。简洁的界面，严谨的文字，总是让人看得那么沁人心脾，
没错，这就是我的博客！我到底做了什么？

是的，我要做的事情就是要让docker容器给我输出这个

```
LiveReload address: http://0.0.0.0:35729
    Server address: http://0.0.0.0:4000/
```

只要给bundle exec jekyll serve -l这个命令加上参数-H 0.0.0.0，我就可以
在外部浏览器去访问docker expose出来的端口。这里要注意的一点是，除了
4000端口要映射出来以外，35729这个用于livereload的端口也要映射出来。

有了docker，我就可以安心地在后台跑服务了。妈妈再也不用担心我的屏幕上放
着多余的terminal了。


## 完整部署命令

```sh
docker run -it  -p 4000:4000 -p 35729:35729 -v $(pwd):/site --name jekyll bretfisher/jekyll-serve bundle exec jekyll serve -l -H 0.0.0.0
```
