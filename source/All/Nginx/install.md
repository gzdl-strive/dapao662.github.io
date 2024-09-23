---
title: Nginx安装
date: 2021-07-31 18:46:24
toc: true
---

## 环境准备
>1. VMware Workstation
>2. CentOS 7 虚拟机
>3. MobaXterm

(1)确认centos的内核
>准备一个内核为2.6及以上版本的操作系统,因为linux2.6及以上内核才支持epoll,而Nginx需要解决高并发压力问题是需要用到epoll，所以我们需要有这样的版本要求。
我们可以使用`uname -a`命令来查询linux的内核版本

(2) 确保centos能联网
`ping www.baidu.com`

(3)确认关闭防火墙
这一项的要求仅针对于那些对linux系统的防火墙设置规则不太清楚的，建议大家把防火墙都关闭掉，因为我们此次课程主要的内容是对Nginx 的学习，把防火墙关闭掉，可以省掉后续Nginx学习过程中遇到的诸多问题。(比如我们在虚拟机中启动了nginx服务器，在我们的window浏览器中输入ip地址打不开，被虚拟机的防火墙拦截了)
关闭的方式有如下两种：
>1.	`systemctl stop firewalld` 关闭运行的防火墙，系统重新启动后，防火墙将重新打开
>2.	`systemctl disable firewalld` 永久关闭防火墙，，系统重新启动后，防火墙依然关闭
>3. `systemctl status firewalld`  查看防火墙状态

(4)确认停用selinux
sestatus查看状态
`setstatus`
如果查看不是disabled状态，我们可以通过修改配置文件来进行设置,修改`SELINUX=disabled`，然后重启下系统即可生效。
>`vim /etc/selinux/config`

![selinux](/assets/nginxImg/selinux.jpg "selinux")

## Nginx安装方式介绍
Nginx安装方式有两种，分别是：
>1. 通过Nginx源码
  >>通过Nginx源码简单安装
  >>通过Nginx源码复杂安装
>2. 通过yum安装

### 通过nginx源码安装需要提取准备的内容
>1. GCC编译器
Nginx是使用C语言编写的程序，因此想要运行Nginx就需要安装一个编译工具。GCC就是一个开源的编译器集合，用于处理各种各样的语言，其中就包含了C语言。
使用命令`yum install -y gcc`来安装
安装成功后，可以通过`gcc --version`来查看gcc是否安装成功

>2. PCRE
Nginx在编译过程中需要使用到PCRE库(`perl Compatible Regular Expressoin` 兼容正则表达式库)，因为在Nginx的Rewrite模块和http核心模块都会使用到PCRE正则表达式语法。
使用命令`yum install -y pcre pcre-devel`进行安装
通过`rpm -qa pcre pcre-devel`来查看是否安装成功

>3. zlib
zlib库提供了开发人员的压缩算法，在Nginx的各个模块中需要使用gzip 压缩，所以我们也需要提前安装其库及源代码zlib和zlib-devel 
使用命令`yum install -y zlib zlib-devel`来进行安装安装成功后
可以通过`rpm -qa zlib zlib-devel`来查看是否安装成功

>4. OpenSSL
OpenSSL是一个开放源代码的软件库包，应用程序可以使用这个包进行安全通信，并且避免被窃听。
可以使用命令`yum install -y openssl openssl-devel`来进行安装
安装成功后，可以通过`rpm -qa openssl openssl-devel`来查看是否安装成功

上述命令，一个个来的话比较麻烦，我们也可以通过一条命令来进行安装
`yum install -y gcc pcre pcre-devel zlib zlib-devel openssl openssl-devel`进行全部安装。

### 方案一、通过Nginx源码简单安装
(1)进入官网查找需要下载版本的链接地址，然后使用wget命令进行下载
```js
wget http://nginx.org/download/nginx-1.16.1.tar.gz
```
(2)建议把下载的资源进行包管理
```js
//创建nginx文件夹，下面有一个core文件夹
mkdir -p nginx/core
//把下载好的nginx.tar.gz压缩包移动到里面取
mv nginx-1.16.1.tar.gz nginx/core
```
(3) 解压缩
```js
cd nginx/core
tar -xzf nginx-1.16.1.tar.gz
```
(4)进入资源文件，发现configure
```js
cd nginx-1.16.1/
./configure
```
(5)编译
```js
make
```
(6)安装
```js
make install
```
完成上述步骤就按照好了，nginx默认的安装路径是/usr/local/nginx下
```js
cd /usr/local/nginx
```
![nginx1](/assets/nginxImg/nginx1.png "nginx1")
进入sbin目录
```js
cd sbin
```
![nginx2](/assets/nginxImg/nginx2.png "nginx2")
执行./nginx
```js
./nginx
```
然后我们就可以在window浏览器中输入我们的虚拟机的ip地址即可，例如本机`http://192.168.193.128/`
**如果打开不了的话，可能是被防火墙拦截了**
我们需要关闭防火墙
```js
systemctl status firewalld//查看防火墙状态
systemctl stop firewalld//关闭防火墙(重启虚拟机后又会重新开启防火墙)
```
