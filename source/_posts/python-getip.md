---
title: Python 优雅的获取本机 IP 地址
categories: 技术
tags:
  - PYTHON
comments: true
date: 2018-01-04 20:10:05
keywords: ip
---

> 在通过 Python 启用 UPnP 网络注册服务时，需要获取本机 IP 地址后才可运行，
> 本文将分享 Python 获取本机 IP 的方法

实验环境：
* `Python：V3.6.3`
* `OS: Ubuntu_12.04 / Ubuntu_17.10 / OS_X_10.11.6`


### 方法一：通过 hostname 获取

``` python
import socket
print(socket.gethostbyname(socket.gethostname()))
```
该方法采用 hostname 查询网络地址，较简单但不可靠。以 Ubuntu 17.10 实测为例，因为系统 `/etc/host` 文件对 hostname 进行了配置，会返回以下结果：

``` python
>>> import socket
>>> print(socket.gethostbyname(socket.gethostname())) 
127.0.0.1
```

### 方法二：通过 UDP 包获取

``` python
import socket

udp = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
try:
    udp.connect(('8.8.8.8', 8991))
    IP = udp.getsockname()[0]
except OSError:
    print('Network is unreachable')
    IP = 'Null'
finally:
    udp.close()
    print(IP)
```

此方法会生成一个 UDP Socket 并从 UDP 协议头中获取本机 IP。不会真实的向外部发包，但是会申请具体的 UDP 端口，注意避免重复调用。

**需要注意确保在系统 network 就绪后方可获取 IP，改进如下：**

``` python
import socket

def get_ip():
    global IP
    udp = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    try:
        udp.connect(('8.8.8.8', 8991))
        IP = udp.getsockname()[0]
    except OSError:
        print('Network is unreachable')
        IP = 'Null'
    finally:
        udp.close()

wait_network = True
IP = 'Null'
while wait_network:
    get_ip()
    if IP != 'Null':
        wait_network = False
    time.sleep(3)
print(IP)
```
