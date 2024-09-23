---
title: 目录结构
date: 2021-08-04 09:34:24
toc: true
---

在使用Nginx之前，我们先对安装好的Nginx目录文件进行一个分析，在这块给大家介绍一个工具tree，通过tree我们可以很方面的去查看centos系统上的文件目录结构，当然，如果想使用tree工具，就得先通过`yum install -y tree`来进行安装，安装成功后，可以通过执行`tree /usr/local/nginx` (tree后面跟的是Nginx的安装目录)，获取的结果如下：
![结构](/assets/nginxImg/structure.png "目录结构")

>`CGI(Common Gateway Interface)`通用网关【接口】，主要解决的问题是从客户端发送一个请求和数据，服务端获取到请求和数据后可以调用调用CGI【程序】处理及相应结果给客户端的一种标准规范。

- conf: nginx所有配置文件目录
 - fastcgi.conf: fastcgi相关配置文件
 - fastcgi.conf.default: fastcgi.conf的备份文件
 - fastcgi.params: fastcgi的参数文件
 - fastcgi_params.default: fastcgi的参数备份文件
 - scgi_params:scgi的参数文件
 - scgi_params.default：scgi的参数备份文件
 - uwsgi_params: uwsgi的参数文件
 - uwsgi_params.default: uwsgi的参数备份文件
 - mime.types: 记录的是HTTP协议中的Content-Type的值和文件后缀名的对应关系
 - mime.types.default: mime.types的备份文件
 - nginx.conf:**这个是Nginx的核心配置文件，这个文件非常重要**
 - nginx.conf.default:nginx.conf的备份文件
 - koi-utf、koi-win、win-utf这三个文件都是与编码转换映射相关的配置文件，用来将一种编码转换成另一种编码
- html: 存放nginx自带的两个静态html页面
  - index.html: 成功访问的默认首页
  - 50x.html: 访问失败后的失败页面
- logs: 记录日志的文件，当nginx服务器启动后，这里会有access.log和error.log和nginx.pid三个文件
 - access.log 访问日志(可以使用命令tail -f access.log查看，当我们输入上述命令后，在浏览器刷新一下nginx页面，就会在终端中再次打印)
 - error.log 错误日志(tail -f error.log和上述一样，当我们在浏览器中输入一个错误的地址就会打印一条日志，例如(ip地址/abc))
 - nginx.pid master进程的pid(可以使用more nginx.pid命令查看)
- sbin: 是存放执行程序文件的目录
 - nginx：是一个二进制可执行文件，用来控制Nginx的启动和停止等相关的命令.


