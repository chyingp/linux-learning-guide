## 写在前面

在linux的日常管理中，find的使用频率很高，熟练掌握对提高工作效率很有帮助。

find的语法比较简单，常用参数的就那么几个，比如`-name`、`-type`、`-ctime`等。初学的同学直接看第二部分的例子，如需进一步了解参数说明，可以参考find的帮助文档。

find语法如下：

>find(选项)(参数)

## 常用例子

### 根据文件名查找

列出当前目录以及子目录下的所有文件

```powershell
find .
```

找到当前目录下名字为`11.png`的文件

```powershell
find . -name "11.png"
```

找到当前目录下所有的`jpg`文件

```powershell
find . -name "*.jpg"
```

找到当前目录下的`jpg`文件和`png`文件

```powershell
find . -name "*.jpg" -o -name "*.png"
```

找出当前目录下不是以`png`结尾的文件

```powershell
find . ! -name "*.png"
```

### 根据正则表达式查找

备注：正则表示式比原先想的要复杂，支持好几种类型。可以参考[这里](http://www.gnu.org/software/findutils/manual/html_mono/find.html#emacs-regular-expression-syntax)

找到当前目录下，文件名都是数字的`png`文件。

```powershell
find . -regex "\./*[0-9]+\.png" 
```

### 根据路径查找

找出当前目录下，路径中包含`wysiwyg`的文件/路径。

```powershell
find . -path "*wysiwyg*"
```

### 根据文件类型查找

通过`-type`进行文件类型的过滤。

* f 普通文件 
* l 符号连接 
* d 目录 
* c 字符设备 
* b 块设备 
* s 套接字 
* p Fifo

举例，查找当前目录下，路径中包含`wysiwyg`的文件

```powershell
find . -type f -path "*wysiwyg*"
```

### 限制搜索深度

找出当前目录下所有的`png`，不包括子目录。

```powershell
find . -maxdepth 1 -name "*.png"
```

相对应的，也是`mindepth`选项。

```powershell
find . -mindepth 2 -maxdepth 2 -name "*.png"
```

### 根据文件大小

通过`-size`来过滤文件尺寸。支持的文件大小单元如下

* b —— 块（512字节） 
* c —— 字节 
* w —— 字（2字节） 
* k —— 千字节 
* M —— 兆字节 
* G —— 吉字节

举例来说，找出当前目录下文件大小超过100M的文件

```powershell
find . -type f -size +100M 
```

### 根据访问/修改/变化时间

支持下面的时间类型。

* 访问时间（-atime/天，-amin/分钟）：用户最近一次访问时间。 
* 修改时间（-mtime/天，-mmin/分钟）：文件最后一次修改时间。 
* 变化时间（-ctime/天，-cmin/分钟）：文件数据元（例如权限等）最后一次修改时间。

举例，找出1天内被修改过的文件

```powershell
find . -type f -mtime -1
```

找出最近1周内被访问过的文件

```powershell
find . -type f -atime -7
```

将日志目录里超过一个礼拜的日志文件，移动到`/tmp/old_logs`里。

```
find . -type f -mtime +7 -name "*.log" -exec mv {} /tmp/old_logs \;
```

注意：{} 用于与-exec选项结合使用来匹配所有文件，然后会被替换为相应的文件名。

另外，`\;`用来表示命令结束，如果没有加，则会有如下提示

```powershell
find: -exec: no terminating ";" or "+"
```

### 根据权限

通过`-perm`来实现。举例，找出当前目录下权限为`777`的文件

```powershell
find . -type f -perm 777
```

找出当前目录下权限不是644的php文件

```powershell
find . -type f -name "*.php" ! -perm 644
```

### 根据文件拥有者

找出文件拥有者为`root`的文件

```powershell
find . -type f -user root
```

找出文件所在群组为`root`的文件

```powershell
find . -type f -group root
```

### 找到文件后执行命令

通过`-ok`、和`-exec`来实现。区别在于，`-ok`在执行命令前，会进行二次确认，`-exec`不会。

看下实际例子。删除当前目录下所有的`js`文件。用`-ok`的效果如下，删除前有二次确认

```powershell
➜  find find . -type f -name "*.js" -ok rm {} \;
"rm ./1.js"? 
```

试下`-exec`。直接就删除了

```powershell
find . -type f -name "*.js" -exec rm {} \;
```

### 找出空文件

例子如下

```powershell
touch {1..9}.txt
echo "hello" > 1.txt
find . -empty
```
