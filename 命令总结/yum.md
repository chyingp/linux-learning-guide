## 简介

软件包管理器，用来管理RPM软件包，如下载、依赖管理。类似前端熟悉的npm。

## 选项说明

>-h：显示帮助信息；
> -y：对所有的提问都回答“yes”； 
> -c：指定配置文件； 
> -q：安静模式； 
> -v：详细模式； 
> -d：设置调试等级（0-10）； 
> -e：设置错误等级（0-10）； 
> -R：设置yum处理一个命令的最大等待时间； 
> -C：完全从缓存中运行，而不去下载或者更新任何头文件。

## 命令

* install：安装rpm软件包
* update：更新rpm软件包
* check-update：检查是否有可用的更新rpm软件包
* remove：删除指定的rpm软件包
* list：显示软件包的信息
* search：检查软件包的信息
* info：显示指定的rpm软件包的描述信息和概要信息
* clean：清理yum过期的缓存
* shell：进入yum的shell提示符
* resolvedep：显示rpm软件包的依赖关系
* localinstall：安装本地的rpm软件包
* localupdate：显示本地rpm软件包进行更新
* deplist：显示rpm软件包的所有依赖关系。

### 安装包

例子，安装`yum-fastestmirror`。

因为官方的源比较慢，这个插件可以自动帮我们选择最快的源。首先安装`yum-fastestmirror`

```powershell
[root@localhost ~]# yum install yum-fastestmirror
```

然后查看配置文件`/etc/yum/pluginconf.d/fastestmirror.conf`。

```powershell
[root@localhost ~]# less /etc/yum/pluginconf.d/fastestmirror.conf
```

内容如下。可以看到，镜像的路径就在`hostfilepath`指定的文件里。我们可以将常见的比较快的源写到里面去。[参考](http://blog.chinaunix.net/uid-20344928-id-2950721.html)

```
[main]
enabled=1
verbose=0
always_print_best_host = true
socket_timeout=3
#  Relative paths are relative to the cachedir (and so works for users as well
# as root).
hostfilepath=timedhosts.txt
maxhostfileage=10
maxthreads=15
#exclude=.gov, facebook
#include_only=.nl,.de,.uk,.ie
```

### 软件升级

升级全部

```powershell
yum update
```

升级特定软件

```powershell
yum update package
```

### 搜索

比如说要安装一个包，但又不记得准确的名字，就可以用`yum search`。比如我要安装nodejs，但是不知道具体是叫做`node`，还是`nodejs`，还是其他的名字，那就搜索一下。

```powershell
yum search nodejs
```

### 显示

显示已安装、可安装的包

```powershell
yum list
```

如下所示，可以看到包的状态，如`installed`、`updates`

```powershell
newt-python.x86_64                      0.52.15-4.el7                  @anaconda
nodejs.x86_64                           1:4.4.7-2nodesource.el7.centos @nodesource
nodesource-release.noarch               el7-1                          installed
```

显示指定程序包的情况。如下所示

```powershell
[root@localhost tmp]# yum list nodejs
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: centos.ustc.edu.cn
 * extras: mirrors.163.com
 * updates: centos.ustc.edu.cn
Installed Packages
nodejs.x86_64                                     1:4.4.7-2nodesource.el7.centos                                      @nodesource
```

### 查看包信息

如果安装了个包，比如`nodejs`，但是不知道这是干嘛用的，那就可以`info`一下。比如

```powershell
yum info nodejs
```

### 删除

```powershell
yum install nodejs
```

### 查看依赖

```powershell
[root@localhost tmp]# yum deplist nodejs
```

### 清除缓存

```powershell
[root@localhost tmp]# yum clean nodejs
```

### 配置文件

路径为`/etc/yum.conf`.

```powershell
[root@localhost yum.repos.d]# less /etc/yum.conf 
[main]
cachedir=/var/cache/yum/$basearch/$releasever
keepcache=0
debuglevel=2
logfile=/var/log/yum.log
exactarch=1
obsoletes=1
gpgcheck=1
plugins=1
installonly_limit=5
bugtracker_url=http://bugs.centos.org/set_project.php?project_id=23&ref=http://bugs.centos.org/bug_report_page.php?category=yum
distroverpkg=centos-release
```

### 修改源

## 相关链接

yum命令
http://man.linuxde.net/yum

yum install 与 yum groupinstall 的区别
http://blog.csdn.net/jiangzhengdong/article/details/8037159

linux yum命令详解
http://www.cnblogs.com/chuncn/archive/2010/10/17/1853915.html


http://cn.linux.vbird.org/linux_basic/0520rpm_and_srpm_4.php