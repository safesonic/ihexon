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



