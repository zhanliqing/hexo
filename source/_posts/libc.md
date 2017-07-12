---
title: 误删linux下的libc.so.6
date: 2017-06-22 17:30:01
tags:
---

今天想装一个软件，由于这个软件需要依赖glibc>=2.14，当前系统的glibc最大是2.12，所以就下载源码、编译升级了，然后就在命令行下执行了如下命令

```shell
sudo mv /lib64/libc.so.6 /lib64/libc.so.6.bak
sudo ln -s /lib64/libc-2.14.so /lib64/libc.so.6

结果报错：
error while loading shared libraries: libc.so.6: cannot open shared object file: No such file or directory

```

此时，大部分的命令都执行不了了，只能执行一些简单的如，cd 等命令，而且ssh也没法登陆了，如果你是远程ssh登陆的服务器，执行完上边的命令后，链接还是存在的，如果是以root用户登陆的，则很好回复，只需要用

```shell
LD_PRELOAD=/lib64/libc-2.14.so ln -s libc-2.14.so libc.so.6
```
就可以建立软连接了，原理就是linux调用so的库文件时，搜素路径为当前路径，再是系统lib目录。但如果设置LD_PRELOAD了后,库加载的顺序就改为LD_PRELOAD ，当前路径，再是系统lib目录。

或者可以执行

```shell
ldconfig
```
来恢复，他会重新建一个软连接，来链接你重命名的那个软链
参考https://stackoverflow.com/questions/12249547/how-to-recover-after-deleting-the-symbolic-link-libc-so-6
如果不是root用户，就乖乖的用光盘修复吧，暂时没查到相关的修复方法
https://superuser.com/questions/267096/how-to-restore-lib-libc-so-6

http://wbwk2005.blog.51cto.com/2215231/415185
