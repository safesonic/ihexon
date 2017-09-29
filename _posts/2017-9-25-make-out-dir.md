---
title: 和我一起编写Makefile
categories: Develop
tags: Makefile教程
---

- 保持源文件目录结构清晰，就需要把各种文件分类存放，便于日后维护。如何编写Makefile，一次性编译所有的存放在`src`目录下的源代码（.c），并将目标（.o）文件输出`obj`文件夹下？

- 所有源代码[FILEYZE](https://github.com/ihexon/fileyze)在此找到。

- 首先列出目录结构

```
zzh@localhost:~$ tree src/
src/
├── changes.html
├── LICENSE
├── LICENSE_ZH_CN
├── Makefile
├── obj ---------> Our objs dir
├── README.md
├── src ---------> Sources dir
│   ├── color.c
│   ├── hash.c
│   ├── html.c
│   ├── json.c
│   ├── strverscmp.c
│   ├── tree.c
│   ├── tree.h
│   ├── unix.c
│   └── xml.c
└── tree.bin

2 directories, 16 files
```

- 首先贴出`Makefile`

```
# fileyze: a tool to list file size timestamp owner/user/groups and etc information in tree like Output
# orign http://mama.indstate.edu/users/ice/tree

SRC_FILES := $(wildcard src/*.c) 
# Include all C sources files in dir src/
OBJ_FILES := $(addprefix obj/,$(notdir $(SRC_FILES:.c=.o)))
# Output all objs into dir obj/
CFLAGS=-ggdb -Wall -DLINUX -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64
LD_FLAGS= -s

all:tree.bin

tree.bin: $(OBJ_FILES)
	   $(CC) $(CFLAGS) $(LD_FLAGS) -o $@ $^

obj/%.o: src/%.c
	   $(CC) $(CFLAGS) $(CC_FLAGS) -c -o $@ $<

clean:
rm -f obj/*
```

我们看一看`SRC_FILES`这个变量后面的`wildcard`，这是一个`Makefile`的内置函数(Functions for File Names)，`wildcard`函数将会包含`src/`目录下的所有C文件，换句话，`SRC_FILES`将会展开成为:

`src/xml.c src/html.c src/strverscmp.c src/unix.c src/hash.c src/json.c src/color.c src/tree.c`

再来看看`OBJ_FILES`后面的`addprefix`函数，该函数将在展开后的`$(notdir $(SRC_FILES:.c=.o))`添加`obj/`这个前缀，比如`$(notdir $(SRC_FILES:.c=.o))`第一次展开是`xml.o`，那么`$(addprefix obj/,$(notdir $(SRC_FILES:.c=.o)))`的结果就是`obj/xml.o`。

关键是`$(addprefix obj/,$(notdir $(SRC_FILES:.c=.o)))`中还有一个`$(notdir $(SRC_FILES:.c=.o))`的变量需要在`addprefix`起作用之前展开，那么`notdir`是干啥的？这个变量应该是中式英文很好理解
就是**不要(not)目录(dir)**，不要什么目录，当然是不要前面的目录路径啦！ 加入`$(SRC_FILES:.c=.o)`展开来是`src/xml.o`那么加上`notdir`就是`xml.o`，真的是**notdir**了....

关键是还有一个`$(SRC_FILES:.c=.o)`又是啥？ 这个好理解，就是把所有的.c换成.o，比如你要把cpp换成cxx，就是`$(SRC_FILES:.cpp=.cxx)`。

`OBJ_FILES`经过层层展开，首先替换后缀名，再去掉前面的src目录名，再加上obj目录名，最终会展开成

`obj/xml.o obj/html.o obj/strverscmp.o obj/unix.o obj/hash.o obj/json.o obj/color.o obj/tree.o`。

CFLAGS和LD\_FLAGS是编译参数用于编译制定平台的源文件和连接器参数。这里不多解释...

`all:tree.bin`作为默认执行make的动作。

`tree.bin: $(OBJ_FILES)` 说明`tree.bin`的生成依赖于`$(OBJ_FILES)`，那么`$(OBJ_FILES)`
也就是`obj/xml.o obj/html.o obj/strverscmp.o obj/unix.o obj/hash.o obj/json.o obj/color.o obj/tree.o`这几个OBJ文件那里找到？

此时`$(OBJ_FILES)`没有生成，所以Makefile会自动处理依赖，Make会接着往下读取Makefile，发现了`obj/%.o: src/%.c`一行

```
obj/%.o: src/%.c
    ¦  $(CC) $(CFLAGS) $(CC_FLAGS) -c -o $@ $<

```

这段代码告诉make:所有obj/[xml],[tree],[...] .o下面的文件依赖于src/[xml],[tree],[...].c下的文件，之后make开始调用GCC编译器编译每个C文件...
`$@`是一种自动化变量，它指向目标文件，即为`obj/%.c`，`$<`指向依赖文件，即为`src/%.c`，
比如第一次展开`$@`和`$<`将会变成`obj/xml.o`和`src/xml.c`，即为`obj/xml.o`依赖于`obj/xml.c`

```
 $(CC) $(CFLAGS) $(CC_FLAGS) -c -o $@ $<
```

真正执行起来应该是这样的:
```
第一次展开...
cc -ggdb -Wall -DLINUX -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64  -c -o obj/xml.o src/xml.c
第二次展开...
cc -ggdb -Wall -DLINUX -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64  -c -o obj/html.o src/html.c
第三次，第四次....
.....
```
再看看，目标文件已经完美的被存放在obj目录下了...

接下来只要链接目标文件生成tree.bin可执行文件就OK

```
tree.bin: $(OBJ_FILES)
	   $(CC) $(CFLAGS) $(LD_FLAGS) -o $@ $^
```

`$^`也是自动化变量，但是他和`$<`不一样，`$^`指向`tree.bin`所有依赖的\*.o文件，即为`obj/xml.o obj/html.o obj/strverscmp.o obj/unix.o obj/hash.o obj/json.o obj/color.o obj/tree.o`

编译指令真正处理起来应该是这样的：

```
cc -ggdb -Wall -DLINUX -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64 -s -o tree.bin obj/xml.o obj/html.o obj/strverscmp.o obj/unix.o obj/hash.o obj/json.o obj/color.o obj/tree.o
```


好，收工，找女朋友去！！
