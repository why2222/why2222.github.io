---
layout:     post
titlt:      在Java Web中添加MySQL驱动
subtitle:   在Java web项目中添加MySQL驱动时遇到的一些小问题
date:       2019-07-13
author:     WHY
header-img: img/post-bg-debug.png
catalog:    true
tag:
    - Java Web
---
# 前言
在一个Java web项目中，需要用到MySQL数据库，但是在向项目添加MySQL的jar时，遇到了一点问题，在这里记录一下。
# 正文
## 1.下载jar包
### 1.1 查看MySQL版本
```shell
mysql -V
mysql  Ver 8.0.15 for osx10.14 on x86_64 (Homebrew)
```
### 1.2 下载对应版本的jar包
我这里使用的是MySQL 8.0.15 for mac，可以从官网下载到对应版本的[MySQLjar包](https://dev.mysql.com/downloads/file/?id=484819)
## 2.添加jar包
将刚才下载好的jar包添加到WEB-INF/libs目录下，然后右击jar包选择*Build Path->add to Build Path。*然后查看根目录下的Referenced Libraries文件中是否有刚刚添加的jar包，如果有，则添加成功。
## 3.问题与解决
在完成上述操作后，运行项目，发现还是无法找到MySQL驱动。这是因为我们需要在tomcat中添加刚刚的使用的jar包。具体操作方法：打开tomcat服务器的安装目录，进入lib目录，将刚才下好的驱动复制进来。重新运行一遍程序，发现运行成功。