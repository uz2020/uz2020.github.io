---
layout: post
title: linux下粘贴板
---

有三种粘贴板
1. PRIMARY
2. SECONDARY
3. CLIPBOARD

Windows下只支持CLIPBOARD。而Linux下支持PRIMARY和CLIPBOARD。对于用户，区别并不重要。

copy文字时，有两种方法
1. 鼠标选中
2. 选中后Ctrl + c

鼠标选中后，文字会copy到PRIMARY selection中。Ctrl + c之后，文字会copy到CLIPBOARD selection中。

paste文字有三种方法
1. Mouse-2
2. Shift + insert
3. Ctrl + v

在特定的编辑器下还有
1. Ctrl + y (emacs)
2. p (vim)
3. Ctrl + Alt + v (urxvt)

所以导致了非常混乱的局面。一些用PRIMARY，一些用CLIPBOARD。所以就有必要使用clipboard manager：autocutsel。它是一个同步PRIMARY和CLIPBOARD的工具。使用时需要跑两个后台进程。

```bash
autocutsel -s PRIMARY -f # 同步PRIMARY
autocutsel -f # 同步CLIPBOARD (由于是默认的selection，所以不用-s参数指定)
```
