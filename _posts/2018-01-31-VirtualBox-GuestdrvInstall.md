---
title: VirtualBox 增强功能（GuestDriver）安装
categories: Develop
tags:
  - VirtualBox
  - Linux
---

## Virtualbox GuestDriver

GuestDriver本质上是一组驱动模块，随VirtualBox二进制包发行，Linux，Windows，BSD，MacOs等平台上的GuestDriver都被打包在一个ISO文件中，当用户点击菜单：
```
  Devices-->Insert Guest Additions CD image
```

该ISO自动插入到虚拟机中的光驱中供用户安装。

这个额外的驱动模块提供系统与虚拟机共享剪贴板，共享文件夹，拖放等额外功能。这篇文档描述在物理机ArchLinux上虚拟Debian unstable操作系统，并在虚拟机内安装GuestDriver。

## 构建Modules所需的依赖

Linux 平台上的 GuestDriver 需要根据内和版本关系手动构建（\>\_\< Linux宏内和的锅），需要安装基本构建环境：`gcc,g++,make,linux-headers`。

Debian：
```
sudo apt install gcc g++ make linux-headers-$(uname -r)
```

NOTE： `linux-headers-$(uname -r)` 返回的是当前版本内和对应的内和头文静，如当前版本内和是4.12.9，则需要安装的内核头文件是linux-headers-4.12.9。其他发行版也如此，安装不对应的内核头文件会导致安装失败。

## 开始构建GuestDriver

插入虚拟ISO文件：

```
Devices-->Insert Guest Additions CD image
```

挂载文件系统：

```
mount /dev/cdrom /mnt
```

复制安装文件进临时目录，赋予可执行权限，并执行run文件

```
cp /mnt/VBoxLinuxAdditions.run /tmp/
chmod /tmp/VBoxLinuxAdditions.run

/tmp/VBoxLinuxAdditions.run
```

输出如下：

```
root@debian:~# ./VBoxLinuxAdditions.run
Verifying archive integrity... All good.
Uncompressing VirtualBox 5.2.7 Guest Additions for Linux........
VirtualBox Guest Additions installer
Copying additional installer modules ...
Installing additional modules ...
VirtualBox Guest Additions: Building the VirtualBox Guest Additions kernel modules.
VirtualBox Guest Additions: Starting.
```

lsmod命令检测时候加载驱动:

```
root@debian:~# lsmod
Module                  Size  Used by
vboxvideo              49152  1
ttm                   114688  1 vboxvideo
drm_kms_helper        192512  1 vboxvideo
drm                   438272  4 vboxvideo,ttm,drm_kms_helper
vboxsf                 53248  0
vboxguest             311296  1 vboxsf
......
```
