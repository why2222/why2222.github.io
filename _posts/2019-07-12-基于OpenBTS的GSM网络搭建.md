---
layout:     post
title:      基于OpenBTS的GSM网络搭建
subtitle:   本文将介绍在Linux系统下基于OpenBTS的GSM网络的搭建。
date:       2019-07-12
author:     WHY
header-img: img/post-bg-debug.png
catalog:    true
tag:
    - OpenBTS
---

# 前言
在```Linux```系统下，通过网上的开源项目[OpenBTS](http://openbts.org/)实现GSM网络搭建。这里使用的```Linux```是```Ubuntu14.04```发行版，对于```Ubuntu16```也可以使用，但是在启动```OpenBTS```时，因为版本问题需要启动脚本才可以使用。下面我们就开始```GSM```网络的搭建吧。

# 正文
## 1.搭建环境
### 1.1 查看系统版本
```shell
lsb_release -a
```
### 1.2 更新和安装依赖
```shell
sudo apt-get update
sudo apt-get -y install git swig cmake doxygen build-essential libboost-all-dev libtool libusb-1.0-0 libusb-1.0-0-dev libudev-dev libncurses5-dev libfftw3-bin libfftw3-dev libfftw3-doc libcppunit-1.13-0 libcppunit-dev libcppunit-doc ncurses-bin cpufrequtils python-numpy python-numpy-doc python-numpy-dbg python-scipy python-docutils qt4-bin-dbg qt4-default qt4-doc libqt4-dev libqt4-dev-bin python-qt4 python-qt4-dbg python-qt4-dev python-qt4-doc python-qt4-doc libfftw3-bin libfftw3-dev libfftw3-doc ncurses-bin libncurses5 libncurses5-dev libncurses5-dbg   libfontconfig1-dev libxrender-dev libpulse-dev swig g++ automake autoconf libtool python-dev libfftw3-dev libcppunit-dev libboost-all-dev libusb-dev libusb-1.0-0-dev fort77 libsdl1.2-dev python-wxgtk2.8 git-core libqt4-dev python-numpy ccache python-opengl libgsl0-dev python-cheetah python-mako python-lxml doxygen qt4-default qt4-dev-tools libusb-1.0-0-dev libqwt5-qt4-dev libqwtplot3d-qt4-dev pyqt4-dev-tools python-qwt5-qt4 cmake git-core wget libxi-dev gtk2-engines-pixbuf r-base-dev python-tk liborc-0.4-0 liborc-0.4-dev libasound2-dev python-gtk2 libzmq1 libzmq-dev python-requests python-sphinx libcomedi-dev
```
## 2.安装UHD
### 2.1 创建项目文件夹
```shell
mkdir gsm
```
### 2.2 下载UHD源码
以下有两种途径可以获取```UHD```源码，[官网](http://files.ettus.com/binaries/uhd_stable/src/)和[GitHub](https://github.com/EttusResearch/uhd.git)。这里推荐使用```GitHub```上的3.9系列的版本，经测试可以成功运行。这里使用的```SDR```为[Ettus](http://www.ettus.com.cn/product/B210kit.html)的B210系列 。
```shell
# GitHub下载源码
git clone git clone -b UHD-3.9.6 https://github.com/EttusResearch/uhd.git
```
### 2.3 编译安装
```shell
cd gsm/uhd
mkdir build & cd build
cmake ../
make -j<cpu线程数>
sudo make install
sudo ldconfig
```
### 2.4 测试
将电脑与```USRP```连接，在终端输入:
```shell
sudo uhd_find_devices
```
可以看到硬件信息。接着在终端输入：
```shell
sudo uhd_usrp_probe
```
可以查看到具体的模块信息。如果出现不兼容的问题，可以根据提示下载镜像。
```shell
sudo /usr/local/lib/uhd/utils/uhd_images_downloader.py
```
## 3.安装GNURadio
### 3.1 下载GNURadio源码
和```UHD```相同，获取```GNURadio```源码可以从[官网](https://www.gnuradio.org/releases/gnuradio/)和[GitHub](https://github.com/gnuradio/gnuradio/releases/tag/v3.7.13.4)上获取。这里推荐使用官网下载的源码。
### 3.2 编译安装
将下载好的源码解压，下面开始编译安装：
```shell
cd gnuradio
mkdir build
cd build
cmake ../
make -j<cpu 线程数>
sudo make install
sudo ldconfig
```
### 3.3 测试
在终端输入：
```shell
sudo gnuradio-companion
```
如果可以成功打开```GNURadio```，则安装成功。
## 4.安装OpenBTS
### 4.1 下载源码
```shell
git clone https://github.com/RangeNetworks/dev.git
```
### 4.2 编译安装
```shell
cd dev
./clone.sh
./switchto.sh master
./build.sh B210
```
*注意：如果系统是```Ubuntu14```的版本，在安装依赖时会报错：找不到```libzmq5```。解决方法：在```./build.sh```之前，可以打开```build.sh```文件将```libzmq5```所在行注销或改为```libzmq3```。*

在编译成功后，编译好的包会放在```~/dev/BUILD```目录下，按照时间找到最新的一个文件夹并进入该文件夹，然后执行：
```shell
sudo dpkg -i *.deb
```
*注意：这里在安装时，可能会因为缺少依赖而报错。解决方法：```sudo apt-get isntall -f```。*
### 4.3 运行OpenBTS
```shell
sudo start sipauthserve
sudo start smqueue
sudo start asterisk
sudo start openbts
```
如果上述组件都能被成功启动，分配到进程号，说明```OpenBTS```安装成功。停止命令如下：
```shell
sudo stop sipauthserve
sudo stop smqueue
sudo stop asterisk
sudo stop openbts
```
启动```OpenBTS CLI```：
```shell
cd /OpenBTS
sudo stop openbts
sudo ./OpenBTS
```
出现```OpenBTS>```，则证明运行成功，在终端输入```help```可以查询指令。