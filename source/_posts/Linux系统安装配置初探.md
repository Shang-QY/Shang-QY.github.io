---
title: Linux系统安装配置初探
date: 2021-05-27 23:47:26
tags:
---

## 一、网卡驱动问题

新拿到一台微星的十代主机，是实验室学长留下的，但这台主机的遗留问题是无法上网。

学长研究后定位问题应该是网卡驱动与网卡不匹配造成的（在stack overflow上发现使用相同网卡会出现类似问题）。

关键问题在于：刚装的Linux系统除了网卡和驱动外，不包含gcc、make等编译套件，这就形成了环形依赖——

- 从网上下载的驱动源码因为没有make不能安装
- gcc等工具的常见安装方法 ```apt get``` 需要联网才能使用

#### 尝试过程：

- 尝试使用ubuntu启动盘中自带的gcc等组件作为 ```apt get``` 的源，而不是通过网络下载。[具体参考]("https://blog.csdn.net/weixin_36452379/article/details/116906829")。但尝试后无法成功，怀疑原因可能是ubuntu桌面版不包含gcc等组件，而博客中的解决的是ubuntu server版的问题。由于重新下载server版，制作启动盘等过程较慢，也没有自己的U盘，因此没有继续尝试重装server版。
- 尝试离线安装gcc。没有gcc是无法离线安装网卡驱动的根源，但gcc的离线安装非常困难，需要预装大量依赖，而每个依赖的安装又可能需要其他很多依赖，可能会遇到循环依赖的问题。[具体参考]("https://blog.csdn.net/weixin_42432439/article/details/108777302?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-8&spm=1001.2101.3001.4242")。由于过程繁复，成功率低，没有尝试。

#### 最终解决：

最终经同学介绍我发现，电脑可以通过USB数据线连接手机，使用手机的共享网络。[具体参考]("https://jingyan.baidu.com/article/5552ef47a4a14c518ffbc99b.html")。连接成功后ubuntu右上角出现网络连接的图标，可在命令行中查看网络设备。

于是，安装了基本的开发工具后，又顺利的安装了网卡对应的驱动（安装驱动[具体参考]("https://blog.csdn.net/hellohake/article/details/109908363")），通过```ifconfig``` 查看网络连接状态，可发现以太网连接成功，至此，无法上网的问题全部解决。

#### 知识总结：

- 面对网络故障且缺少其他资源时，应首先尝试其他联网方法，包括wifi网卡，usb共享网络的方法等。
- 在linux kernel中会自带网卡驱动模块，包含了常用网卡的驱动源码，最近的内核版本中都包含了r8169模块，ubuntu20.04的kernel版本为5.8.0，可通过```uname```命令查看。
- 设备管理的命令包括：```lspci -vv```   ```lsmod```  等，驱动相关命令有 ```insmod```。



## 二、重装系统过程

使用 ```dd``` 命令制作Linux系统启动盘。[具体参考]("https://www.dreamoftime0.com/2018/09/25/linux下使用dd命令制作启动盘/")。

