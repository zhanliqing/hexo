---
title: 编译安装微软开源lightgbm工具
date: 2017-06-26 15:37:58
tags: 
- 机器学习
- linux
categories: 机器学习
---

lightgbm在Linux下安装需要glibc>=2.14，大部分centos系统默认的安装版本是glibc=2.12。因此，要想再上边安装，就需要升级glibc，我们知道glibc是一个很底层的调用库，升级之后容易出现很多错误。一个安全的安装方法是，我在系统上边安装多个glibc版本呢，在编译运行lightgbm的时候用新的版本glibc，因为lightgbm使用了c++11的一些特性，通常情况下也需要升级gcc编译器，同样可以安装多个版本的gcc编译器，下面是我的操作过程：
1、下载gcc和glibc的源码，编译，过程在网上搜一下，需要注意的是，编译的时候指定prefix为你要安装的目录，不要覆盖系统上的已有的，如

```shell
make prefix=/home/q/users/liqing.zhan/gcc/ install
```

2、设置LD_LIBRARY_PATH

```shell
export LD_LIBRARY_PATH=/home/q/users/liqing.zhan/lib:/home/q/users/liqing.zhan/gcc/lib64/:/lib64/
```
这里说一下LD_LIBRARY_PATH和LIBRARY_PATH的区别，这两个都linux的环境变量，LIBRARY_PATH环境变量用于在程序编译期间查找动态链接库时指定查找共享库的路径，例如，指定gcc编译需要用到的动态链接库的目录。设置方法如下（其中，LIBDIR1和LIBDIR2为两个库目录）：

```shell
export LIBRARY_PATH=LIBDIR1:LIBDIR2:$LIBRARY_PATH
```
LD_LIBRARY_PATH环境变量用于在程序加载运行期间查找动态链接库时指定除了系统默认路径之外的其他路径，注意，LD_LIBRARY_PATH中指定的路径会在系统默认路径之前进行查找。设置方法如下（其中，LIBDIR1和LIBDIR2为两个库目录）：

```shell
export LD_LIBRARY_PATH=LIBDIR1:LIBDIR2:$LD_LIBRARY_PATH
```

https://stackoverflow.com/questions/4250624/ld-library-path-vs-library-path    
https://typecodes.com/cseries/gcclderrlibrarypath.html    

如果用export来设置，只是当前回话有效的，可以在~/.bash_profile下设置

3、设置gcc和g++的路径

```
export CC=/usr/local/bin/gcc
export CXX=/usr/local/bin/g++
```

https://stackoverflow.com/questions/17275348/how-to-specify-new-gcc-path-for-cmake     
https://stackoverflow.com/questions/11588855/how-do-you-set-cmake-c-compiler-and-cmake-cxx-compiler-for-building-assimp-for-i    


4、编译lightgbm