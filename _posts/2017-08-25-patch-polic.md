---
title: Patch 元信息描述规范
---


大多数Patch系统允许在文件头部进行自由描述(日期，作者，功能填等...)。
我提议以一种更规范并且结构化的方式进行Patch元信息描述。

## 为啥

为了提高软件开发质量，对Patch质量审核非常重要，为了能一眼就看出Patch文件对我们的代码做了啥(日期/作者/注释等...)，以一种结构化的描述Patch元信息非常有利于大型项目开发，有利于BugTracker西游有条不紊的运作。

## 结构
### 注释

```
<字段>
--- a/foo 
+++ b/foo 
```

`<字段>` 在patch头部的任何文字将被忽略。

```bash
# 字段一
# 字段二
# 字段三
......
```

`#`之后的字段也将被忽略，`#`后接着一位空格，`#`前面不能有空格。

### 元信息关键字

- Vendor

`<Vendor>`字段可用于描述描述patch使用平台，基本格式`<Architecture>_<OS>_<ETC>`，如`ARMEL_DEBIAN，AARCH_UBUNTU，X86-64_Window7`，不限制大小写，简明即可。

- Description或subject

```
Description: Use FHS compliant paths by default Upstream is not interested in switching to those paths. But we will continue using them in Debian nevertheless to comply with our policy.


Subject: Fix regex problems with some multi-bytes characters * posix/bug-regex17.c: Add testcases. * posix/regcomp.c (re_compile_fastmap_iter): Rewrite COMPLEX_BRACKET handling. 

```

Description用于进行简短的描述。

Subject可用于更加详细的说明patch文件细节，如应用于某函数，修改某模块，补丁历史等...

如果patch曾被拒绝，也需要在该字段中描述。简明即可。

- Origin或Author

```
Author: John Doe <johndoe-guest@users.alioth.debian.org>

Origin: upstream, http://sourceware.org/git/?p=glibc.git;a=commitdiff;h=bdb56bac
```

这个字段应该给出patch的来源或者作者，如果patch来源CVE上游源码，那么请给出CVS页面地址(包含commit ID号)。如果patch来自邮件列表，BugTracker，等，那么只需给出URL地址即可。

该字段可选进行patch类别描述，如，实例来源于upstream

可选类别为：upstream，backport，vendor(厂商)，other(其他类别)。
各人创建的patch应该归类为other类。从其他商场复制patch时，元信息应该被保留。

如果为个人创建的patch，那么请使用Author关键字。如果patch为多人维护，那么联系人可添加，联系人邮箱附在姓名之后，并用<>包裹

- Bug-\<Vendor\>或Bug(可选)

```
Bug: http://sourceware.org/bugzilla/show_bug.cgi?id=9697 

Bug-Debian: http://bugs.debian.org/510219
```

该字段包含上游BugTracker跟踪到的Bug所在的URL，如果有多个bug，这些字段可以被重复使用

Vendor见**关键字Vendor**

- Reviewed-by或Acked-by(可选)

该字段记录代码审核人员信息，该字段使用与Author字段同样，如多个人审核Patch，则可以多次添加人员信息(以行为单位)

- Last-Update(可选)

记录patch元信息最后更新时间，格式为标准ISO格式：`YY-MM-DD`

- Applied-Upstream(可选)

```
Applied-Upstream: 1.2, http://bzr.example.com/frobnicator/trunk/revision/123 Last-Update: 2010-03-29
```

该字段记录patch应用于上游源码详细信息，包含应用这个patch的上游源码版本，BugTracker的URL(以URL:开头)，commit记录等(以commit:开头)
