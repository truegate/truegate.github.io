---
title: python 获取工程路径
categories: 技术
tags:
  - PYTHON
comments: true
date: 2018-01-16 18:03:03
keywords: getcwd
---

通过 `import os` ，使用 `os.path.abspath(os.path.dirname(__file__))` 获取绝对路径。

<!-- more -->

在工程项目的 `config.py` 中往往需要获取项目路径。

> 结论：通过 `import os` ，使用 `os.path.abspath(os.path.dirname(__file__))` 获取绝对路径。

### 对比 os.getcwd() 

`os.getcwd()` 依赖启动环境的 `$pwd` 指令结果，返回的是 **脚本启动路径** 而非存储路径。当然，常常配合 `os.chdir()` 使用。

例如，有 `/home/pi/test.py` 文件：

``` python
#!/bin/python3
import os

path1 = os.getcwd()
path2 = os.path.abspath(os.path.dirname(__file__))

print("cwd:", path1)
print("file:", path2)
```
在 `/home/` 下运行结果如下：

``` bash
cwd: /home/
file: /home/pi
```

在 `/home/pi/` 下运行结果如下：

``` bash
cwd: /home/pi
file: /home/pi
```
