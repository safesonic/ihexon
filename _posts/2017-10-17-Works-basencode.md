---
title: Resume-Me!
layout: splash
---

# Work简历 #

## Info ##

我叫**朱智浩**

```
 Phone:13177815083
 Email:zzheasy@gmail.com
 Wechat:pleasechangeme
 QQ:1836308192
 HomePages:
 https://ihexon.github.io
 GitHub:
 https://github.com/ihexon
```

## 经历 ##

我喜欢和一群志同道合的小伙伴，一起分析解决问题。

习惯熬夜逛GitHub，Kernel mail list，Debian mail archive，RFC documents，国内开源社区。

对Linux有着信仰式的狂热，有着强烈求知欲和好奇心，喜欢解决复杂问题并享受折腾不息的过程。

不想过安逸的生活，只想不断折腾，被虐，然后学习，再被虐，再学习...

## 都会什么 ##

**都比较简单初级**

运维相关：
 - LAMP架构
 - Linux网络，HTTP(S)Proxy，tun2socks，DNServer
 - 简单Docker容器应用

CTF&安全相关:
 - 隐写
 - 简单apk逆向
 - 锐捷网关渗透
 - 简单二进制分析
 - 近场RFDI无线攻防
 
操作系统相关：
 - ELF ABI规范结构
 - 巨复杂的GNU构建系统
 - 略知编译原理
 - 基础操作系统原理（Unix like）

## 做过什么 ##
 - **YourWebApps**

    一个人做的GemsRuby&Jekyll运行在Android`/data/local/tmp/`位置下。
裁剪RUBY2.3-git及其依赖并艰难的向Android移植，艰难的重新打包了所有依赖GEM使得Jekyll能在Android有限的环境下稳定运行，整个过程就是以艰难来形容。
**刚开始我对Ruby&GEM一无所知，很长一段时间都在看Ruby的构建系统和GEM结构文档**，已经记不清我碰见了多少奇怪的问题...
Ruby+Gem是一个灵活的架构，有很多GEM没有包含本地C代码，就不需要重新打包，所以不仅Jekyll可以运行，还可以运行其他ruby的小型服务程序。
我的Blog就是在YourWebAPPs下生成静态页面，进行测试，并定期自动push至Github，真的很好用。

 - **DroidVim**

    我为什么要移植Vim给Android,因为在Busybox自带的Vim太弱了，我想要在adb shell里使用更加友好可扩展的Vim，这样在进行二进制分析能更加顺手，Vim+Vinarise是一个非常强大的HEX编辑器。
我遇到的NDK自带Curses不能通过Terminfo数据库初始化Xterm仿真，导致CR/LF异常，控制模式下控制位异常，由于Xterm终端控制特性巨复杂，**有230多页的文档**，我无法通过修改Terminfo结构解决问题，但后来我简单粗暴的将Ncurses链接至Vim二进制文件，隔离了Android自带Curses...

这样一个**Vim+Vinarise做一个HEX编辑器**，非常很好用...
```    
Vim:Feature 
    +lua +python2 +python3 +perl +ruby
```
 - **Ettercap On Android**

    Ettercap是一个arp欺骗工具，由于其Cmake构建系统，很容易将其向Android移植，这样就能在Android上进行局域网一番欺骗（笑一笑罢了），XDA上已经有人移植了arpspoof，但我喜欢造轮子....
一直有人问我怎么做到一台让Android手机变成网关的。
Ettercap提供了很多so插件，现在能加载的有dns,arp,string,sniff几个插件，有些插件依赖于其他代码库。

 - **NodeJS on Android**

  Nodejs在github上已经有人开始移植，起初我拿来练习Javascript，但我发现nodejs能做的太多了，也许可以进一步剪裁优化 ,让Android具有nodejs runtime&API 0.10.X的能力，比如
```
> adb shell am start -a android.intent.action.VIEW -n com.iwebpp.nodeandroid/.MainActivity -e js "var run = function () { return 'hello world'; } run();"
```
 - **ETC...**

     研究了Android系统启动流程和具体行为，分区作用，AndroidBuildSystem，于是替换掉了自己手机的Kernel，这个Kernel加入了我通过Kconfig注入的NFC，自定义USB—HID设备，RTL系列，VIDEO-V4L2通用设备驱动，这样就可以打造手持无限渗透设备，放在包包里，商场走一圈，可以截取到Public WiFi的所有数据包，甚至是Cookie,图片，明文密码。
     

  - **我还玩硬件**

![Noah0](/images/1508329165955.jpeg)

JZ4740 CPU，MIPSEL，QPE Desktop，Noah 1500数学王

开源硬件，动手DIY（偏向软件...）
