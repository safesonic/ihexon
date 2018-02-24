---
title: Linux内核模块签名
categories: Develop
layout: splash
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

执行`make ARCH=arm64 SUBARCH=arm64 CROSS_COMPILE=aarch64-linux-gnu-`后内核自动生成三个文件

```
x509.genkey # Key generation configuration file
signing_key.priv # Pri certs
signing_key.x509 # Pub certs
```

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

# 使用自定义 x509.genkey 生成证书签名

我们还可以使用openssl自己生成一对密钥文件:

新建x509.genkey文件用于生成密钥对，文件内容如下：

```
zzh@localhost:~/angler$ cat x509.genkey
[ req ]
default_bits = 4096
distinguished_name = req_distinguished_name
prompt = no
string_mask = utf8only
x509_extensions = myexts

[ req_distinguished_name ]
O = Magrathea
CN = Glacier signing key
emailAddress = slartibartfast@magrathea.h2g2

[ myexts ]
basicConstraints=critical,CA:FALSE
keyUsage=digitalSignature
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid
```

用openssl生成密钥对：

`openssl req -new -nodes -utf8 -sha512 -days 36500 -batch -x509 -config x509.genkey -outform DER -out signing_key.x509 -keyout signing_key.priv`

同样会输出`x509.genkey，signing_key.priv，signing_key.x509`三个文件。

  - 使用密钥对给未签名的密钥对签名
  在`meke moudles_install`时，Makefile调用一个外置PERL程序在安装阶段给模块签名，外置命令使用`signing_key.pri，signing_key.x509`这俩个文件给模块签名，如果我们使用自定义密钥对重新编译了内核，则需要手动给旧的模块签名。

  PERL签名脚本存放在内核源码树目录下：`scripts/sign-file`

  此命令传入四个参数：<加密算法> <私有密钥> <公钥> <内核模块名>

  手动执行签名：`scripts/sign-file sha512 kernel-signkey.priv kernel-signkey.x509 zlib.ko`

  In the end , reload the signatured kernel modules.

# 验证内核时候签名：

  - 验证内核时候签名：
      带有签名的内核在文件末尾有数字签名，使用hexedit或者xxd查看文件末尾验证。

      `xxd cn.ko`:

  ```
  00001450  9d eb d4 05 58 f5 1c 72  52 96 60 91 f3 a7 71 63  |....X..rR.`...qc|
  00001460  9e ef dc a4 43 52 63 14  eb 9a f4 45 5a 57 5c 5a  |....CRc....EZW\Z|
  00001470  8d 27 80 94 9f 2b 61 3d  26 3c 5c 3f 5b 3c 86 49  |.'...+a=&<\?[<.I|
  00001480  aa d5 14 34 6f ae 04 92  1b c1 87 30 6c fc 36 9a  |...4o......0l.6.|
  00001490  22 c1 07 35 21 8e 6b 38  9d fd ca 95 86 ba 4f 50  |"..5!.k8......OP|
  000014a0  e3 d2 c6 19 b5 84 e1 07  9a 75 4a 03 01 06 01 1e  |.........uJ.....|
  000014b0  14 00 00 00 00 00 02 02  7e 4d 6f 64 75 6c 65 20  |........~Module |
  000014c0  73 69 67 6e 61 74 75 72  65 20 61 70 70 65 6e 64  |signature append|
  000014d0  65 64 7e 0a                                       |ed~.|
  000014d4
  "]'`
  ```

  同时，我们还可以去掉数字签名：

```
zzh$ strip --strip-debug zlib.ko
zzh$ hexdump -C crc8.ko
```

```
  000011c0  00 00 00 00 00 00 00 00  58 0a 00 00 00 00 00 00  |........X.......|
  000011d0  c7 01 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
  000011e0  01 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
  000011f0  11 00 00 00 03 00 00 00  00 00 00 00 00 00 00 00  |................|
  00001200  00 00 00 00 00 00 00 00  f8 0c 00 00 00 00 00 00  |................|
  00001210  b6 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
  00001220  01 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  |................|
  00001230
```

# 自签名内核用于UEFI - Security Boots
  [Booting a Self-signed Linux Kernel](http://www.kroah.com/log/blog/2013/09/02/booting-a-self-signed-linux-kernel/)
