---
title: Linux 开机自启程序
categories: 技术
tags:
  - LINUX
comments: true
date: 2018-01-04 22:17:02
keywords: linux service
---

在开发智能硬件产品时，经常需要在 Linux 系统下，设置程序随系统启动而自动运行。
**本文总结了常用的三种方法**

<!-- more -->

> 在开发智能硬件产品时，经常需要在 Linux 系统下，设置程序随系统启动而自动运行。
> 本文总结了常用的三种方法

实验环境：
* `OS: Ubuntu_12.04 / Ubuntu_17.10 / Dietpi_V154 / Raspbian_8`

## 方法一：借助 systemct 工具管理


## 方法二：借助 init.d 实现

`init.d` 目录维护了系统的诸多初始化服务，具体参阅：

* [Update-rc.d - install and remove System-V style init script links](http://manpages.ubuntu.com/manpages/xenial/man8/update-rc.d.8.html) 

* [LSBInitScripts](https://wiki.debian.org/LSBInitScripts) 

**详细步骤**

**第一步 创建 service 文件**
``` bash
$ vim /etc/init.d/homebase  # Fedora系统目录路径在 /etc/rc.d/init.d/
```
内容如下，`Required-Start` 项指明了启动前置条件

> **Dietpi_V154 Kernel 3.4.113** ~~Required-Start: $network~~ 测试失败

``` bash
#!/bin/bash
# /etc/init.d/homebase
    
### BEGIN INIT INFO
# Provides: Quentin
# Required-Start: $network $remote_fs $syslog
# Required-Stop: $network $remote_fs $syslog
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: homebase gateway
# Description: This service is used to start ssdp and jsonrpc serice for homebase
### END INIT INFO
    
case "$1" in
    start)
        echo "Starting Homebase SSDP"
        python3 /root/homebase-ssdp/homebase.py &
        ;;
    stop)
        echo "Stopping Homebase SSDP"
        kill $( ps aux | grep -m 1 'python3 /root/homebase/homebase.py' | awk '{ print $2 }' )
        ;;
    *)
        echo "Usage: service Homebase SSDP start|stop"
        exit 1
        ;;
esac
exit 0
```

**第二步 增加可执行权限**

``` bash
$ chmod +x /etc/init.d/homebase
```

**第三步 设置开机自启**

``` bash
$ update-rc.d homebase defaults
 ```

**第四步 service 管理技巧**

可以随时通过 `service XXX start|stop|restart` 控制程序状态，还可以通过status查看服务的详细运行信息
``` bash
$ service XXX status    # 查看服务状态
```

## 方法三：借助 rc.local 实现入口
`/etc/rc.local` 是系统初始化完成后会执行的脚本，可以安全的在里面添加自定义程序的启动入口，这种方法最为常用。
该文件的格式一般为：

``` bash
#!/bin/bash
...
...
exit 0
```

入口指令需要添加在 `exit 0` 之前。
