---
title: Nginx服务器启停命令
date: 2021-08-04 09:51:24
toc: true
---

>Nginx的启停在linux系统中也有很多种方式，我们在这边介绍两种方式：
>1. Nginx服务的信号控制
>2. Nginx的命令行控制

## 方式一:Nginx服务的信号控制
>Nginx中的master和worker进程? 
Nginx的工作方式? 
如何获取进程的PID? 
信号有哪些? 
如何通过信号控制Nginx的启停等相关操作?

Nginx默认采用的是多进程的方式来工作的，当将Nginx启动后，我们通过`ps -ef | grep nginx`命令可以查看到如下内容：
![process](/assets/nginxImg/process.png "进程")
从上图中可以看到,Nginx后台进程中包含一个master进程和多个worker进程，master进程主要用来管理worker进程，包含接收外界的信息，并将接收到的信号发送给各个worker进程，监控worker进程的状态，当worker进程出现异常退出后，会自动重新启动新的worker进程。而worker进程则是专门用来处理用户请求的，各个worker进程之间是平等的并且相互独立，处理请求的机会也是一样的。
![process](/assets/nginxImg/process_model.png "进程模型")
我们现在作为管理员，只需要通过给master进程发送信号就可以来控制Nginx,这个时候我们需要有两个前提条件，一个是要操作的master进程，一个是信号。
（1）要想操作Nginx的master进程，就需要获取到master进程的进程号ID。获取方式简单介绍两个
方式一：通过`ps -ef | grep nginx`；
方式二：`logs/nginx.pid`文存放的就是master进程ID
（2）信号
| 信号 | 作用 |
|:------:|:-----:|
| TERM/INT | 立即关闭整个服务 |
| QUIT | “优雅”地关闭整个服务 |
| HUP | 重读配置文件并使用服务对新配置项生效 |
| USR1 | 重新打开日志文件，可以用来进行日志切割 |
| USR2 | 平滑升级到最新版的nginx |
| WINCH | 所有子进程不在接收处理新连接，相当于给worker进程发送QUIT指令 |
调用命令为`kill -signal PID`(signal:即为信号；PID即为获取到的master线程ID)

## 方式二、Nginx的命令行控制
此方式是通过Nginx安装目录下的sbin下的可执行文件nginx来进行Nginx状态的控制。
-?和-h:显示帮助信息
-v:打印版本号信息并退出
-V:打印版本号信息和配置信息并退出
-t:测试nginx的配置文件语法是否正确并退出
-T:测试nginx的配置文件语法是否正确并列出用到的配置文件信息然后
退出
-q:在配置测试期间禁止显示非错误消息
-s:signal信号，后面可以跟 ：
stop[快速关闭，类似于TERM/INT信号的作用] kill -WINCH PID /kill -WINCH`cat /usr/local/nginx/logs/nginx.pid` 1
quit[优雅的关闭，类似于QUIT信号的作用] 
reopen[重新打开日志文件类似于USR1信号的作用] 
reload[类似于HUP信号的作用]
-p:prefix，指定Nginx的prefix路径，(默认为: /usr/local/nginx/)
-c:filename,指定Nginx的配置文件路径,(默认为: conf/nginx.conf)
-g:用来补充Nginx配置文件，向Nginx服务指定启动时应用全局的配置