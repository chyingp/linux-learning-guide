## 写在前面

在linux服务器的日常管理中，xargs是使用率非常高的一个命令，借助xargs的帮助，我们可以轻松完成很多原本很繁琐的工作。本文对xargs做个入门介绍，同时举一些常见的例子来进行讲解。

## 命令简介

xargs可以将输入内容（通常通过命令行管道传递），转成后续命令的参数，通常用途有：

1. 命令组合：尤其是一些命令不支持管道输入，比如`ls`。
2. 避免参数过长：xargs可以通过`-nx`来将参数分组，避免参数过长。

## 使用语法

```
Usage: xargs [OPTION]... COMMAND INITIAL-ARGS...
Run COMMAND with arguments INITIAL-ARGS and more arguments read from input.
```

## 入门例子

首先，创建测试文件

```shell
touch a.js b.js c.js
```

接着，运行如下命令：

```bash
ls *.js | xargs ls -al
```

输出如下：

```bash
-rw-r--r--  1 a  wheel  0 12 18 16:18 a.js
-rw-r--r--  1 a  wheel  0 12 18 16:18 b.js
-rw-r--r--  1 a  wheel  0 12 18 16:18 c.js
```

命令解释：

1. 首先，`ls *.js`的输出为`a.js b.js c.js`。
2. 通过管道，将`a.js b.js c.js`作为`xargs`的输入参数。
3. `xargs`命令收到输入参数后，对参数进行解析，以空格/换行作为分隔符，拆分成多个参数，这里变成`a.js`、`b.js`、`c.js`。
4. `xargs`将拆分后的参数，传递给后续的命令，作为后续命令的参数，也就是说，组成这样的命令`ls -al a.js b.js c.js`。

可以加上`-t`参数，在执行后面的命令前，先将命令打印出来。

```bash
ls *.js | xargs -t ls -al
```

输出如下，可以看到多了一行内容`ls -al a.js b.js c.js`，这就是实际运行的命令。

```bash
ls -al a.js b.js c.js
-rw-r--r--  1 a  wheel  0 12 18 16:18 a.js
-rw-r--r--  1 a  wheel  0 12 18 16:18 b.js
-rw-r--r--  1 a  wheel  0 12 18 16:18 c.js
```

## 例子：参数替换

有的时候，我们需要用到原始的参数，可以通过参数`-i`或`-I`实现。参数说明如下

```bash
  -I R                         same as --replace=R (R must be specified)
  -i,--replace=[R]             Replace R in initial arguments with names
                               read from standard input. If R is
                               unspecified, assume {}
```

例子如下，将所有的`.js`结尾的文件，都加上`.backup`后缀。`-I '{}'`表示将后面命令行的`{}`替换成前面解析出来的参数。

```bash
ls *.js | xargs -t -I '{}' mv {} {}.backup
```

展开后的命令如下：

```bash
mv a.js a.js.backup
mv b.js b.js.backup
mv c.js c.js.backup
```

## 例子：参数分组

命令行对参数最大长度有限制，xargs通过`-nx`对参数进行分组来解决这个问题。

首先，创建4个文件用来做实验。

```bash
touch a.js b.js c.js d.js
```

然后运行如下命令：

```bash
ls *.js | xargs -t -n2 ls -al
```

输出如下，`-n2`表示，将参数以2个为一组，传给后面的命令。

```bash
ls -al a.js b.js 
-rw-r--r-- 1 root root 0 Dec 18 16:52 a.js
-rw-r--r-- 1 root root 0 Dec 18 16:52 b.js
ls -al c.js d.js 
-rw-r--r-- 1 root root 0 Dec 18 16:52 c.js
-rw-r--r-- 1 root root 0 Dec 18 16:52 d.js
```

## 例子：特殊文件名

有的时候，文件名可能存在特殊字符，比如下面的文件名中存在空格。

```
touch 'hello 01.css' 'hello 02.css'
```

运行之前的命令会报错，因为`xargs`是以空格/换行作为分隔符，于是就会出现预期之外的行为。

```bash
# 命令
find . -name '*.css' | xargs -t ls -al

#输出
ls -al ./hello 01.css ./hello 02.css # 展开后的命令
ls: cannot access ./hello: No such file or directory
ls: cannot access 01.css: No such file or directory
ls: cannot access ./hello: No such file or directory
ls: cannot access 02.css: No such file or directory
```

`xargs`是这样解决这个问题的。

1. `-print0`：告诉`find`命令，在输出文件名之后，跟上`NULL`字符，而不是换行符；
2. `-0`：告诉`xargs`，以`NULL`作为参数分隔符；

```bash
find . -name '*.css' -print0 | xargs -0 -t ls -al
```

## 例子：日志备份

将7天前的日志备份到特定目录

```bash
find . -mtime +7 | xargs -I '{}' mv {} /tmp/otc-svr-logs/
```

## 相关链接


https://craftsmanbai.gitbooks.io/linux-learning-wiki/content/xargs.html

http://wiki.jikexueyuan.com/project/shell-learning/xargs.html