---
X系统复制粘贴机制
header:
    overlay_image: ../images/Screenshot_20171010_022010.png
    overlay_filter: 0.5
---

## 以XTERM为例子：

Xterm中的复制和粘贴，只要选择需要复制的文字，然后按`Shift+Insert`就可以粘贴选中的文字。但是只能在XTERM中进行复制粘贴，和XTERM之外的程序不共享剪切板。

这也许有点反人类了....

Xterm使用仅读取`PRIMARY buffer`这个缓冲区里的数据进行复制粘贴，而我们运行在X里的应用程序使用`CLIPBOARD buffer`这个缓冲区数据进行复制粘贴，俩个世界就是隔离的，所以不能交换缓冲区的数据。默认情况下，X里的应用程序选择的文字不能往Xterm里粘贴，Xterm里的文字不在X应用里粘贴....

## Xterm和X应用双向复制粘贴

在`$HOME/.Xresources`里加入

```
XTerm*selectToClipboard: true

```
之后，加载配置文件

```
xrdb -load ~/.Xresources
```
重启XTERM。

## 关于PRIMARY和CLIPBOARD

**PRIMARY buffer**

当用户用鼠标选中高亮文本时，选择的文本数据流向"PRIMARY"缓冲区。 当用户在Xterm中按下鼠标中键时，将粘贴此缓冲区中的文本。 这种剪切和粘贴缓冲区是传统功能，新用户通常不会被告知，以避免大量混乱。 大多数现代应用程序支持此缓冲区。 这是xterm使用的唯一缓冲区。

**CLIPBOARD buffer**

"CLIPBOARD"缓冲区用于大多数用户熟悉的剪切和粘贴：从应用程序的“编辑”菜单中选择“剪切”，“复制”或“粘贴”菜单项，或使用相应的 CTRL-X，CTRL-C或CTRL-V快捷键。 此选择缓冲区是在大多数现代应用程序中执行剪切和粘贴操作的标准方法。 但是，这个缓冲区不幸的是在默认配置下xterm完全没有使用。
