---
title: 'Linux init 详解'
date: Mon, 21 Mar 2022 07:55:18 +0000
draft: false
tags: ['Linux', '笔记']
---

![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b0/NewTux.svg/400px-NewTux.svg.png)

什么是 init
========

init 进程是一个由内核启动的用户级进程

内核自行启动（已经被载入内存，开始运行，并已初始化所有的设备驱动程序和数据结构等）之后，就通过启动一个用户级程序 init 的方式，完成引导进程。所以，init 始终是第一个进程（其进程编号始终为1）。

内核会在过去曾使用过init的几个地方查找它，它的正确位置（对 Linux 系统来说）是 `/sbin/init` 。如果内核找不到 init，它就会试着运行 `/bin/sh` ，如果运行失败，系统的启动也会失败。

运行级别
====

init 一共分为 7 个级别，这 7 个级别的所代表的含义如下：

```
#init 0 - 停机（千万不能把initdefault 设置为 0）
#init 1 - 单用户模式，只由 root 用户进行维护
#init 2 - 多用户，不能使用NFS（Net File System）不联网
#init 3 - 完全多用户模式(标准的运行级)
#init 4 - 安全模式
#init 5 - X11 （xwindow) 图形化界面模式
#init 6 - 重新启动 （千万不要把 initdefault 设置为6 ）
```

开机默认模式设置方法
==========

系统下有个根文件目录：`/etc/inittab`

```
.  # inittab       This file describes how the INIT process should set up
.  #               the system in a certain run-level.
.  #
.  # Author:       Miquel van Smoorenburg, <miquels@drinkel.nl.mugnet.org>
.  #               Modified for RHS Linux by Marc Ewing and Donnie Barnes
.  #
.  # Default runlevel. The runlevels used by RHS are:
.  #   0 - halt (Do NOT set initdefault to this)
.  #   1 - Single user mode
.  #   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
.  #   3 - Full multiuser mode
.  #   4 - unused
.  #   5 - X11
.  #   6 - reboot (Do NOT set initdefault to this)
.  #
.  id:3:initdefault:
```

如果设置为 `id:3:initdefault:` 这代表默认启动为命令行模式。如果设置为 `id:5:initdefault:` 这代表默认 xwindow 图形化界面模式

Debian 系没有 /etc/inittab 文件
==========================

使用命令实现，使用 `apt install sysv-rc-conf` 安装

然后再次执行命令运行：`sysv-rc-conf`

如何查看当前运行级别
----------

使用 `runlevel` 命令查看

```
root@debian:~# runlevel
N 5 
```