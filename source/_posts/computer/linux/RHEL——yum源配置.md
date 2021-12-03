---
title: 红帽7yum源替换
tags:
  - RHEL learning
  - 学习
  - 笔记
categories:
  - 艾小艾的小笔记
url: https://github.com/Aiyuin/Aiyuin.github.io/
date: 2021-03-26 12:00:00
updated: 2021-03-26 17:00:00
---

## Trap

安装yum源：

1. http://mirrors.163.com/

2. http://mirrors.163.com/.help/centos.html

<!-- more -->

---

### 第一步：卸载原有的yum

检查原有的yum包

```
rpm -qa |grep yum
```

卸载原有YUM

```sh
rpm -qa|grep yum|xargs rpm -e --nodeps
```

确认是否卸载完成

> 回车后无信息显示代表OK！

```env
rpm -qa |grep yum
```

---

### 第二步：下载yum源

下载YUM包以及相关依赖文件（6个）

> 创建目标文件夹，进入目录可以直接复制到终端

```sh
wget http://mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/rpm-4.11.3-40.el7.x86_64.rpm

wget http://mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/python-iniparse-0.4-9.el7.noarch.rpm

wget http://mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/python-urlgrabber-3.10-9.el7.noarch.rpm

wget http://mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/yum-3.4.3-163.el7.centos.noarch.rpm

wget http://mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/yum-metadata-parser-1.1.4-10.el7.x86_64.rpm

wget http://mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.31-52.el7.noarch.rpm
```

**此处安装可能会有版本不同出现错误，可以去 [http://mirrors.163.com/centos](https://link.zhihu.com/?target=http%3A//mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/) 找到高一些的版本**

> **Ctrl + F 查找文件的开头即可【[rpm-4.11.3](https://link.zhihu.com/?target=http%3A//mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/rpm-4.11.3-40.el7.x86_64.rpm)】**

---

### 第三部：安装yum

安装顺序必须统一

 [rpm-4.11.3-40.el7.x86_64.rpm](https://link.zhihu.com/?target=http%3A//mirrors.163.com/centos/7.7.1908/os/x86_64/Packages/rpm-4.11.3-40.el7.x86_64.rpm) --nodeps **[\\](https://link.zhihu.com/?target=file%3A//xn--qkr05qtu4aecinra535a/)这个必须最先安装**

rpm -Uvh python-urlgrabber-3.10-9.el7.noarch.rpm --nodeps

rpm -Uvh python-iniparse-0.4-9.el7.noarch.rpm --nodeps

rpm -ivh \\此命令直接会把剩下的所有自动安装

安装后终端输入YUM，看看有没有反应。

```sh
rpm -Uvh （） --nodeps
rpm -ivh
```

---

### 第四步：新建配置.repo文件

```sh
vim /etc/yum.repos.d/CentOS-Base.repo
```

将一下文本复制到.repo文件

```sh
#CentOS-Base.repo

#

# The mirror system uses the connecting IP address of the client and the

# update status of each mirror to pick mirrors that are updated to and

# geographically close to the client. You should use this for CentOS updates

# unless you are manually picking other mirrors.

#

# If the mirrorlist= does not work for you, as a fall back you can try the

# remarked out baseurl= line instead.

#

#

[base]

name=CentOS-$7 - Base - http://163.com

#mirrorlist=http://mirrorlist.centos.org/?release=$7&arch=$basearch&repo=os

baseurl=http://mirrors.163.com/centos/7/os/$basearch/

gpgcheck=1

gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

#released updates

[updates]

name=CentOS-$7 - Updates - http://163.com

#mirrorlist=http://mirrorlist.centos.org/?release=$7&arch=$basearch&repo=updates

baseurl=http://mirrors.163.com/centos/7/updates/$basearch/

gpgcheck=1

gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful

[extras]

name=CentOS-$7 - Extras - http://163.com

#mirrorlist=http://mirrorlist.centos.org/?release=$7&arch=$basearch&repo=extras

baseurl=http://mirrors.163.com/centos/7/extras/$basearch/

gpgcheck=1

gpgkey=http://mirrors.163.com/centos/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages

[centosplus]

name=CentOS-$7 - Plus - http://163.com

baseurl=http://mirrors.163.com/centos/7/centosplus/$basearch/

gpgcheck=1

enabled=0

gpgkey=http://mirrors.163.com/centos/
```

> 注意：也可以去[http://mirrors.163.com/.help/centos.html](http://mirrors.163.com/.help/centos.html)去寻找.repo文件,但是里面的版本配置参数要修改。（$releasever修改为对应的版本号）

---

To be continued.
