---
title: Linux内核模块签名
categories: Develop
tags:
    - Linux
    - Security
    - Kernel
---

# 什么是Linux内核模块签名/验证

Linux内核可选对内核模块进行证书验证，就像Windows受信任驱动程序一样，启用Linux内核模块验证能提高系统内核安全性，阻未经授权的恶意程序以内核模块方式运行。内核签名还用于分发部署私有的内核模块，不过这在开源世界中比较少见。

# 如何开启内核模块验证

Linux内核模块验证默认是关闭的，需要在内核编译阶段进行一些设置。这里我为Nexus 6p内核（代号angler），版本**3.10.107**开启Module Signature verification。
  
  - 内核模块签名验证是内核的一个特性，所以需要在编译阶段开启这个特性，必要的参数在`Enable loadable module support`菜单中给出详细配置
  ```
  --- Enable loadable module support
  [*]   Module signature verification
  [*]     Require modules to be validly signed
  [*]     Automatically sign all modules
        Which hash algorithm should modules be signed with? (Sign modules with SHA-512) --->
  ```
这里我选中`Require modules to be validly signed(CONFIG_MODULE_SIG_FORCE)`，代表所有内核模块必须被有效签名才能加载，内核默认使用宽容设置，代表内核模块不强制要求有效签名。
另一种开启强制验证有效签名的方法是在内核启动参数中传入`enforcemodulesig=1`。

`Automatically sign all modules(CONFIG_MODULE_SIG_ALL)`：为内核在执行make modules\_install时自动签名所有模块，内核默认不自动签名所有模块除非你选中此选项。

`Sign modules with SHA-512`：最后我用SHA-512认证算法。

构建内核成功后，执行`make ARCH=arm64 SUBARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu- INSTALL_MOD_PATH=o
utput modules_install`后，被签名的内核模块在output目录下，新内核启动后，原有的所有内核模块将加载失败，只有在output下经过签名的模块能成功加载。

```
adb shell 
angler$ su

root# insmod ./zlib.ko
modprobe: ERROR: could not insert 'vxlan': Required key not available 
# Fail insmod unsignature module zlib.ko

root# insmod ./output/zlib.ko 
# Succes loading signature module zlib.ko

root# lsmod
Module                  Size  Used by
zlib                    6056  0
```


