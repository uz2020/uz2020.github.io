---
layout: post
title: ssconvert
---

今天干活时发现了ssconvert这个工具，它来自于gnumeric。

有了它，我就不用自己去写go程序解析excel了。

excel数据导入数据库，虽然原理简单，但还是有要注意的地方。

比如有的excel表格里用户填充的数据有换行符，那csv就不能直接用换行符作为行分隔符了。ssconvert支持linux/windows/macos三种系统的换行符。

然后要注意'^M'和'\r\n'的区别。虽然我也没搞明白，但是在sql语句里的LOAD指令中，要这样写：

```sql
LINES TERMINATED BY '\r\n'
```

其次，你永远不知道用户输入的内容会包含什么字符，如果简单地使用逗号作为field separator，那就很容易出问题了。为了circumvent这个问题，我用了一个泰文字符做分隔，也是萬不得已。

先把excel转成csv，再用csv来导入数据库，远比直接写代码去读excel来导入数据库要简单。

最后一个问题是关于引号的。LOAD指令对输入文件的内容不允许对字符串引起来。一开始我是直接用libreoffice来输出csv（libreoffice做这个事情还是比ssconvert弱），它输出的csv中，数据就是直接呈现的，没有引号，无论是整数还是字符串。但ssconvert会自动给字符串加上引号，反倒让LOAD指令无法识别了。另外地，ssconvert的format=raw还是要少用，我用了这个选项以后，那些日期数据都无法导出到csv了。

最后，上一下我用ssconvert时的命令行：

```bash
ssconvert -O "separator=ภ eol=windows quote=" 主机.xlsx 主机.txt
```


