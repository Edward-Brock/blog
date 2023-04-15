---
title: 'Scoop 包管理工具'
date: Sun, 10 Apr 2022 09:31:37 +0000
draft: false
tags: ['Scoop', 'Windows', '笔记']
---

安装步骤
====

打开 PowerShell远程权限
-----------------

```
Set-ExecutionPolicy RemoteSigned -scope CurrentUser;
```

若出现提示是否要更改执行策略?，输入 Y 回车

自定义 Scoop 包安装路径
---------------

```
$env:SCOOP='自定义路径'
[environment]::setEnvironmentVariable('SCOOP',$env:SCOOP,'User')
iwr -useb get.scoop.sh | iex
```

如果跳过该步骤，Scoop 将默认把所有用户安装的 App 和 Scoop 本身置于 `C:\Users\\scoop`

安装 Scoop
--------

```
iwr -useb get.scoop.sh | iex
scoop update
```

或者使用国内镜像：

```
iwr -useb https://gitee.com/glsnames/scoop-installer/raw/master/bin/install.ps1 | iex
scoop config SCOOP_REPO 'https://gitee.com/glsnames/Scoop-Core'
scoop update
```

如果提示错误，说明 PowerShell 需要调整进行一些配置。这时按照提示，输入：

```
Set-ExecutionPolicy RemoteSigned -scope CurrentUser
```

然后重新运行第一条指令即可。

如果发现安装速度极慢，导致安装错误，但再次安装仍提示 scoop 已安装，那么可以输入下面这一行指令来强制删除，然后再重新安装：

```
del .\scoop -Force
```

安装 Scoop 的 bucket
-----------------

安装完毕，但是我们还要再安装一个 scoop 的 bucket。scoop 默认自带的 bucket 是 main，包含大量的没有 GUI 的程序，比如 Node.js，Aria2，Git，FFmpeg 等。如果想要安装带有 GUI 的程序，可以安装名为 extras 的 bucket。

安装 `extras` 很简单，只需要一行指令：

```
scoop bucket add extras
```

如果出现问题，或者不想用这个 `bucket` 了，那么可以用下面这条语句来删除：

```
scoop bucket rm extras
```

操作命令
====

帮助语法
----

```
scoop help
```

安装操作
----

```
scoop install 软件名
```

安装指定Bucket中的应用
--------------

```
scoop install extras/sumatrapdf
```

安装指定版本
------

```
scoop install python@3.7.9
```

版本切换
----

```
scoop reset python
scoop reset python27
```

更新指定应用
------

```
scoop update python
```

禁止更新指定应用
--------

```
scoop hold python
```

解除禁止更新指定应用
----------

```
scoop unhold python
```

更新所有已安装应用
---------

```
scoop update *
```

更新 bucket 库
-----------

```
scoop update
```

清理所有旧版本
-------

```
scoop cleanup *
```

卸载操作
----

```
scoop uninstall 软件名
```

全局卸载(包括persist)
---------------

```
scoop uninstall 软件名 -p
```

常用软件表
-----

```
01.aria2：scoop install aria2
02.everything：scoop install everything
03.cmder：scoop install cmder
04.notepad2：scoop install echo/notepad2
05.q-dir：scoop install q-dir
06.vim：scoop install vim
07.keepass：scoop install keepass
08.chrome：scoop install googlechrome
09.firefox：scoop install firefox
10.vivaldi：scoop install vivaldi
11.opera：scoop install opera
12.python：scoop install python
13.nodejs：scoop install nodejs
14.go：scoop install go
15.trafficmonitor：scoop install trafficmonitor
16.notepadplusplus：scoop install notepadplusplus
17.sublime-text：scoop install sublime-text
18.vscode：scoop install vscode
19.pycharm：scoop install pycharm
20.intellij-idea：scoop install intellij-idea
21.goland：scoop install goland
22.fscapture：scoop install echo/fscapture
23.snipaste：scoop install snipaste
24.synctrayzor：scoop install synctrayzor
25.telegram：scoop install telegram
26.notion：scoop install notion
27typora：scoop install typora
```