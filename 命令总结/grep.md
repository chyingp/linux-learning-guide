## 例子

### 在文件中搜索单词

在`jquery.js`中搜索`event`这个单词。

```powershell
grep "event" jquery.js
```

在多个文件中搜索单词。

```powershell
grep "module" jquery/jquery.js login/login.js 
```

### 搜索反转

在`jQuery.js`中搜索没有`event`的行。

```powershell
grep -v "event" jquery.js
```

### 用正则表达式搜索

在`jQuery.js`中搜索`jquery`或者`jQuery`

```powershell
grep -E "j[Q,q]uery" jquery.js
```

等价于

```powershell
egrep "j[Q,q]uery" jquery.js 
```

### 只输出匹配结果

如果不想将匹配到的整行都打印出来，可以加上`-o`

```powershell
grep -o -E "j[Q,q]uery" jquery.js
```

### 统计匹配的行数

```powershell
➜  jquery grep -c -E "jquery" jquery.js
13
```

### 显示匹配结果的行号

```powershell
➜  jquery grep -n "jQuery" jquery.js
2: * jQuery JavaScript Library v1.9.1
8: * Copyright 2005, 2012 jQuery Foundation, Inc. and other contributors
25: // A central reference to the root jQuery(document)
```

多个文件。结果太长，忽略部分输出。

```powershell
➜  modules grep -n "module" login/login.js jquery/jquery.js
login/login.js:10:module.exports = {
jquery/jquery.js:608:   // Convert dashed to camelCase; used by the css and data modules
jquery/jquery.js:7237:  // rather than make the css module depend on the offset module, we just check for it here
jquery/jquery.js:9581:// Expose jQuery as an AMD module, but only for AMD loaders that
jquery/jquery.js:9585:// specifying define.amd.jQuery = true. Register as a named module,
```

### 递归查找

在当前目录下查找包含`jquery`的文件。

```powershell
grep -n -r "jquery" . 
```

### 多个搜索文本

```powershell
grep -n -e "jquery" -e "event" jquery.js
```

### 包括/排除搜索文件

通过`--include`、`--exclude`实现。其中：

* 指定了`--include`，只有文件路径满足pattern的文件才会被搜索
* 指定了`--exclude`，只有文件路径不满足pattern的文件才会被搜索
* `--exclude`优先级高于`--include`。

> --include
             If specified, only files matching the given filename pattern are searched.  Note that --exclude pat-
             terns take priority over --include patterns.  Patterns are matched to the full path specified, not
             only to the filename component.




```powershell
➜  src grep "body" . -r --include "*.css"
./lib/normalize.css:body {
./modules/app-account/app-operate-record/app.css:html,body,#js-container{
./modules/app-holiday/app-calendar/app.css:html,body,#js-container{
./modules/app-holiday/app-manage/app.css:html,body,#js-container{
./modules/app-index/app.css:html,body,#js-container{
```

### 不显示任何信息

通过`-q`，找到匹配结果，则返回0，否则返回非0值。一般用于条件判断。

```powershell
➜  jquery grep -q "jquery" jquery.js
➜  jquery grep -q "jquery" jquery.js & echo "find jquery"
[1] 9106
find jquery
[1]  + 9106 done       grep --color=auto --exclude-dir={.bzr,CVS,.git,.hg,.svn} -q "jquery" jquery.j  
```

###  打印匹配结果之前、之后的行

假设test.txt有如下内容

```
1
2
3
4
5
6
7
8
9
```

打印出匹配`5`那一行的前3行。

```powershell
➜  grep grep "5" test.txt -B 3
2
3
4
5
```

打印后三行

```powershell
➜  grep grep "5" test.txt -A 3
5
6
7
8
```

前3行和后三行

```powershell
➜  grep grep "5" test.txt -C 3
5
6
7
8
```