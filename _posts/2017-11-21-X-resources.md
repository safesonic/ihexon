---
title: X resources
layout: splash
categories: Develop
tags: 
  - Linux
  - X Windows
---

# 什么是Xresources

**X resources** 是一个用户级别（user-level）的隐藏配置文件，一般情况下位于`~/.Xresources`。使用它来配置X应用程序客户端的各种参数。

它能做许多操作，包括：
  - 定义Terminal的颜色
  - 配置Terminal的偏好设置
  - 设置DPI，字体抗锯齿，提升和其他X字体设置。
  - 改变Xcursor主题
  - 改变了底层X应用的偏好设置（xclock(xorg-xclock,) xpdf, rxvt-unicode,等...）

# 安装
  - 安装xorg-xrdb软件包

# 用法
  - 加载resource文件
  Resources被存储在X server中，所以只需要加载一次。远程X11客户端也可以访问（如通过SSH转发的客户端）。
  加载resources文件（常规~/.Xresources文件），覆盖任何当前的设置：
  
  `$ xrdb ~/.Xresources`

  或者合并当前设置：

  `$ xrdb -merge ~/.Xresources`

  **注意:**
  - 大多数显示管理会在用户登陆时加载`~/.Xresources`
  - 当X11程序启动时，旧的`~/.Xdefaults`文件被读取，但只有在当前会话中没有使用xrdb的情况下。
  
  - **xinitrc**文件
  如果你使用了默认的xinitrc文件的拷贝作为你的.xinitrc，那么它已经合并了`~/.Xresources`的设置。如果你使用定制的.xinitrc，那么添加下面一行到.xinitrc文件中：

  `[[ -f ~/.Xresources ]] && xrdb -merge -I$HOME ~/.Xresources`

  - Resources的默认设置

  要查看已经安装X11应用的默认设置，则在`/usr/share/X11/app-defaults/`查找。
  有关于单个程序的resources设置通常在程序MAN PAGES里给出具体说明，Xterm的MAN PAGES就是一个好例子，包含了一些X resources和它们默认的值。
  查询当前已经加载的的resources：

  `xrdb -query -all`

  - Xresources 书写语法

  基本语法：

  `name.Class.resource: value`

  > name: 应用程序的名称，如xterm,xpdf,xclock等

  > class: 类，将同一resource归类为一组，类通常为大写

  > resource: 需要更改的resources的名称，通常用小写
  
  > value: 更改的值，有三个值，整数，Boolean，字符串

  - 通配府


  星号（\*）表示通配符，使得用一条规则匹配许多不同应用变得容易。如果你想要所有包含类（class）名字Dialog并且resources名
  为headingFont的程序使用同一个字体，你应该这样写规则：

  ```
  *Dialog.headingFont:     -*-fixed-bold-r-*-*-*-100-*-*-*-*-iso8859-1

  ```

  如果想为所有包含resource字段为headingFont的程序设置同一个字体：

  ```
  *headingFont:    -*-fixed-bold-r-*-*-*-100-*-*-*-*-iso8859-1
  ```

  - 注释

  为了添加注释，只需在注释字段前加上 ！ 前缀

  ```
  ! The following rule will be ignored because it has been commented out
  !Xft.antialias:        true
  ```

  - 引用外部文件

  注意：为了在加载过程中宏被成功引入，你需要安装C预编译器，如GNU CPP

  Xresource可以从外部文件加载配置：

  ```
  ~/.Xresources

  #include ".Xresources.d/xterm"
  #include ".Xresources.d/rxvt-unicode"
  #include ".Xresources.d/fonts"
  #include ".Xresources.d/xscreensaver"

  ```

  If files fail to load, specify the directory to xrdb with the -I parameter. For example: 

  ```
  ~/.xinitrc

  xrdb -I$HOME ~/.Xresources

  ```








