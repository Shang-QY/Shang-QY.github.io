---
title: Manjaro安装配置踩坑
date: 2021-06-02 17:32:55
tags:
---



## MacOS制作启动盘

[Mac 制作 Linux 启动盘](https://www.cnblogs.com/sitoi/p/13047622.html)

[mac os x 下查看dd命令刻录U盘的进度](https://blog.csdn.net/weixin_33807284/article/details/92859241)



## Manjaro 21.05 安装和配置

ArchLinux官网：https://archlinux.org/

 - [学习环境配置：Manjaro、MSYS2以及常见软件](https://www.cnblogs.com/wurui1994/p/6279501.html)
 - [最受欢迎的Linux发行版, Manjaro折腾全记录（超长超详细）]("https://www.jianshu.com/p/21c39bc4dd31")
- [manjaro最新搜狗输入法安装教程]("https://www.136.la/java/show-55970.html")
- [Linux(Manjaro) -Docker 安装及基本配置](https://www.cnblogs.com/imzhizi/p/10718310.html)




## Ubuntu 18.04 安装和配置
[Ubuntu 18.04 安装和配置](https://zhuanlan.zhihu.com/p/38364829)



## 源码阅读利器：UnderStand安装与踩坑

### 1）Understand用法及Linux安装指南

 - [Understand: 静态代码分析神器](https://zhuanlan.zhihu.com/p/44776823)
 - [Understand(源码阅读器)Installing on Linux and Solaris](https://support.scitools.com/t/installing-on-linux-and-solaris/34)

### 2）Understand在Manjaro上运行报错的解决

根据上述的Understand官网上的Linux安装指南，在Ubuntu 20.04上可以正常运行，但在Manjaro 21.05上出现如下错误：

在`./understand` 时报错：

​	`./understand: symbol lookup error: /usr/lib/libfontconfig.so.1: undefined symbol: FT_Done_MM_Var`

为此我作出以下调研和尝试：

1. 其他软件的相似问题和解决方式：
 - [Tecplot运行报错:undefined symbol: FT_Done_MM_Var](https://blog.csdn.net/weixin_41174045/article/details/115741968)
 - [Err：undefined symbol：FT_Done_MM_Var 及 .so 文件替换方法](https://blog.csdn.net/qq_45569859/article/details/103341971)
2. Linux使用动态库时的undefined symbol问题：
 - [Linux C编程问题：symbol lookup error: xxx undefined symbol xxx](https://blog.csdn.net/guangyacyb/article/details/85165231?utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-5.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7Edefault-5.control)
 - ["symbol lookup error"问题解决](https://blog.csdn.net/dfman1978/article/details/6104544)
3. LinuxQuestions上的相似问题：
 - [libreoffice libfontconfig.so.1: undefined symbol: FT_Done_MM_Var](https://www.linuxquestions.org/questions/slackware-14/libreoffice-libfontconfig-so-1-undefined-symbol-ft_done_mm_var-4175665794/?__cf_chl_jschl_tk__=369654a585808df0dfb545a61055e86a3c75a6a9-1622624792-0-AdAhDup71USuBhSCHZOhTIyF2Qkc9S7lvAPBCx90jYXf1-1KrPFU2Bu4tF1dQcH1KLNQQMXeVkCkjetRZ2bYxDIngiM26sx6ocPE0HKlyhlp-8VoX_ar9vQqV7M-5sgP8zwHt_AdrPGaCay-ahSIgrYL25MNCS260O4WwvanLcVNOjVc3jlsGZphOymef5vs_SGJy70B5y6fZpGDVQLIM7qizqEhOIt2lP0L1FIIWzu3iRIt7_Ozi7I6LLZlxS2oNb5pBqJULDEJQ2iymBO5q7F6-85iMPfBVCSPGw_6Eq67BbgvH3C8PLaszRAaaYyKk8xTRZbbL2-h47yY8shc_2RcTpaU3JlOKbBX1OW6suFe96CSfvxr1PBUjP1EsE9KSlYtQo0P8fHBH8uGbdVsVAsjT1In4NOPuYeXWRO74RCbe7qS8N10mP7KOPuwt-Q39umxi9Xq_o8B6B0dwCZq9-e65zZEqDQMBAfAw7OKqau0x7SfJWIK6CK9-aGx4dPbuS_skJOmxJNWwN6DuaavTsddgQSqZcwroEFjwdzbtS6s4zBWI3OXS3ilQN3GNDOMtr4dhqaxJT9UYxAaJfZkOAo)

从这些链接中我学到Linux使用动态库时需注意的问题和很多有用的Linux命令（如`ldd` 、`nm` 、`grep` 等），同时也发现自己**对make的过程，Makefile的编写等还不熟悉**，希望后面将这块知识补上。

在调试的过程中我尝试了上述链接中的方法，但manjaro的`/usr/lib/`中 `libfreetype.so` 和`libfontconfig.so`都存在，`/usr/lib/libfontconfig.so`依赖于`/usr/liblib/freetype.so`，且`FT_Done_MM_Var	` 也在`libfreetype.so` 中定义了。而Understand的源码和makefile文件无法找到，因此**不能通过修改编译过程解决**。已知Understand在Ubuntu 18.04上可以正常运行，而Manjaro 21.05与Ubuntu的唯一区别在于Manjaro的Linux系统版本是5.10，而Ubuntu的系统版本是5.8，两系统中`libfontconfig`的版本一致（都是`libfontconfig.so.1.12.0`，而`libfreetype`的版本不同（Manjaro是`libfreetype.so.6.17.4`，Ubuntu是`libfreetype.so.6.17.1`），因此可能的问题应该与第一个链接（[Tecplot运行报错](https://blog.csdn.net/weixin_41174045/article/details/115741968)）相似，**需要降低Linux系统版本**。

Manjaro换内核的帖子（[链接](https://tieba.baidu.com/p/6406292549)），尝试无果。

最后，***将understand安装目录中`/bin/linux64` 下的 `libfreetype.so.6` 删除，运行成功！***

反向思考错误原因，应该是`/bin/linux/libfreetype.so` 版本低，没有定义FT_Done_MM_Var，使用`/bin/linux/libfreetype.so` 时会加载系统的`/usr/lib/libfontconfig.so.1` 动态库。该动态库依赖的是版本较高的`/usr/lib/libfreetype.so`，而后者因为相同的库已存在所以没有加载，至此问题解决。



## Linux命令学习与积累

 - [Linux df 命令](https://www.runoob.com/linux/linux-comm-df.html)
 - [Linux locate命令](https://www.runoob.com/linux/linux-comm-locate.html)
    - [\[SOLVED]Locate command not found](https://bbs.archlinux.org/viewtopic.php?id=99084)
 - [Linux grep 命令](https://www.runoob.com/linux/linux-comm-grep.html)
 - [Linux下nm和ldd命令](https://blog.csdn.net/netwalk/article/details/19335771)



