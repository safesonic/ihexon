---
title: 什么是PIC/PIE代码
categories: Develop
tags: 二进制分析
---

## 简介

PIC： 地址无关代码（position-independent code）
PIE： 地址无关可执行文件（position-independent executable）

PIC/PIE指的是能够在随机内存地址运行而不是在绝对地址运行的代码。PIC/PIE代码被广泛应用于缺少内存管理单元的计算机中。常见的动态共享库便是PIC代码，
所以动态库能在不同的程序内存地址开始加载。
动态库的生成需要在MAKEFILE中加入`-fPIC`参数，生产地址无关代码。

利用readelf查看时候为可执行文件是否为PIE:

```
zzh@localhost:/tmp$ readelf -h test            
ELF Header:                                    
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00                                     
  Class:                             ELF32     
  Data:                              2's complement, little endian                             
  Version:                           1 (current)                                               
  OS/ABI:                            UNIX - System V                                           
  ABI Version:                       0         
  Type:                              DYN (Shared object file)  -----> PIE                              
  Machine:                           ARM       
  Version:                           0x1       
  Entry point address:               0x465     
  Start of program headers:          52 (bytes into file)                                      
  Start of section headers:          9224 (bytes into file)                                    
  Flags:                             0x5000400, Version5 EABI, hard-float ABI                  
  Size of this header:               52 (bytes)                                                
  Size of program headers:           32 (bytes)                                                
  Number of program headers:         9         
  Size of section headers:           40 (bytes)                                                
  Number of section headers:         35        
  Section header string table index: 34        
```

```
zzh@localhost:/tmp$ readelf -d test                                                                                                                                                           

Dynamic section at offset 0xf10 contains 26 entries:                                           
  Tag        Type                         Name/Value                                           
 0x00000001 (NEEDED)                     Shared library: [libc.so.6]                           
 0x0000000c (INIT)                       0x408 
 0x0000000d (FINI)                       0x5d8 
 0x00000019 (INIT_ARRAY)                 0x10f08                                               
 0x0000001b (INIT_ARRAYSZ)               4 (bytes)                                             
 0x0000001a (FINI_ARRAY)                 0x10f0c                                               
 0x0000001c (FINI_ARRAYSZ)               4 (bytes)                                             
 0x6ffffef5 (GNU_HASH)                   0x1b4 
 0x00000005 (STRTAB)                     0x2b0 
 0x00000006 (SYMTAB)                     0x1e0 
 0x0000000a (STRSZ)                      166 (bytes)                                           
 0x0000000b (SYMENT)                     16 (bytes)                                            
 0x00000015 (DEBUG)                      0x0   
 0x00000003 (PLTGOT)                     0x11000                                               
 0x00000002 (PLTRELSZ)                   40 (bytes)                                            
 0x00000014 (PLTREL)                     REL   
 0x00000017 (JMPREL)                     0x3e0 
 0x00000011 (REL)                        0x390 
 0x00000012 (RELSZ)                      80 (bytes)                                            
 0x00000013 (RELENT)                     8 (bytes)                                             
 0x6ffffffb (FLAGS_1)                    Flags: PIE                                            
 0x6ffffffe (VERNEED)                    0x370 
 0x6fffffff (VERNEEDNUM)                 1     
 0x6ffffff0 (VERSYM)                     0x356 
 0x6ffffffa (RELCOUNT)                   6     
 0x00000000 (NULL)                       0x0   
```



在生成地址无关代码需要遵循源代码语义规范，并且编译器需要支持，现代编译器均支持PIC/PIE，并且一般默认生成PIC/PIE代码。

但PIE/PIC会限制某些语言的特性，因为PIE/PIC不能指定内存地址加载，而直接使用绝对地址执行比相对地址快那么一点点（真的只有一点点，可以忽略不计）。
在生成PIE/PIC时，源代码中绝对地址引用会被替换成PC相对寻址。

## PIC/PIE相对安全

PIC/PIE的发明通过随机内存地址加载代码段，增加了攻击者访问特定敏感内存区域的数据进行攻击系统的难度
