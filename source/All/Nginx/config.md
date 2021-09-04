---
title: Nginx核心配置文件
date: 2021-08-04 10:06:24
toc: true
---

>Nginx的配置文件中可以使用#来注释
- nginx.conf配置文件中默认有三大块：全局块、events块、http块
- http块中可以配置多个server块，每个server块又可以配置多个location块

## 全局块
**user指令**
user:用于配置运行Nginx服务器的worker进程的用户和用户组

| 语法 | user user(group) |
|:------:|:-----:|
| 默认值 | nobody |
| 位置 | 全局块 |

**work process指令**
`master_process`: 用来指定是否开启工作进程。

| 语法 | master_process on/off; |
|:------:|:-----:|
| 默认值 | master_process on; |
| 位置 | 全局块 |

`worker_processes`: 用于配置Nginx生成工作进程的数量,这个是Nginx服务器实现并发处理服务的关键所在,理论上来说workder process的值越大，可以支持的并发处理量也越多，但事实上这个值的设定是需要受到来自服务器自身的限制，建议将该值和服务器CPU的内核数保存一致.

**其他指令**
`daemon`: 设定Nginx是否以守护进程的方式启动。
守护式进程是linux后台执行的一种服务进程，特点是独立于控制终端，不会随着终端关闭而停止。

| 语法 | daemon on/off; |
|:------:|:-----:|
| 默认值 | daemon on; |
| 位置 | 全局块 |

`pid`: 用来配置Nginx当前master进程的进程号ID存储的文件路径。

| 语法 | pid file; |
|:------:|:-----:|
| 默认值 | 默认为:/usr/local/nginx/logs/nginx.pid |
| 位置 | 全局块 |

`error_log`: 用来配置Nginx的错误日志存放路径

| 语法 | error_log file [日志级别]; |
|:------:|:-----:|
| 默认值 | error_log logs/error.log error; |
| 位置 | 全局块、http、server、location |

其中日志级别的值有：
`debug|info|notice|warn|error|crit|alert|emerg`，翻译过来为 调试|信息|通知|警告|错误|临界|警报|紧急，这块建议大家设置的时候不要设置成info以下的等级，因为会带来大量的磁盘I/O消耗，影响Nginx的性能。

`include`:用来引入其他配置文件，使Nginx的配置更加灵活

## events块
**accept_mutex**: 用来设置Nginx网络连接序列化

| 语法 | accept_mutex on/off; |
|:------:|:-----:|
| 默认值 | accept_mutex on; |
| 位置 | events |

这个配置主要可以用来解决常说的"惊群"问题。大致意思是在某一个时刻，客户端发来一个请求连接，Nginx后台是以多进程的工作模式，也就是说有多个worker进程会被同时唤醒，但是最终只会有一个进程可以获取到连接，如果每次唤醒的进程数目太多，就会影响Nginx的整体性能。如果将上述值设置为on(开启状态)，将会对多个Nginx进程接收连接进行序列号，一个个来唤醒接收，就防止了多个进程对连接的争抢。

**multi_accept**: 用来设置是否允许同时接收多个网络连接

| 语法 | multi_accept on/off; |
|:------:|:-----:|
| 默认值 | multi_accept off; |
| 位置 | events |

如果multi_accept被禁止了，nginx一个工作进程只能同时接受一个新的连接。否则，一个工作进程可以同时接受所有的新连接 

**worker_connections**: 用来配置单个worker进程最大的连接数

| 语法 | worker_connections number; |
|:------:|:-----:|
| 默认值 | worker_commections 512; |
| 位置 | events |

这里的连接数不仅仅包括和前端用户建立的连接数，而是包括所有可能的连接数。另外，number值不能大于操作系统支持打开的最大文件句柄数量

**use**: 用来设置Nginx服务器选择哪种事件驱动来处理网络消息

| 语法 | use method; |
|:------:|:-----:|
| 默认值 | 根据操作系统定 |
| 位置 | events |

注意：此处所选择事件处理模型是Nginx优化部分的一个重要内容，method的可选值有`select/poll/epoll/kqueue`等，之前在准备centos环境的时候，我们强调过要使用linux内核在2.6以上，就是为了能使用epoll函数来优化Nginx。

**events指令配置实例**
```js
events {
  accept_mutex on;
  multi_accept on;
  worker_commections 1024;
  use epoll;
}
```

## http块
### 定义MIME-Type
我们都知道浏览器中可以显示的内容有HTML、XML、GIF等种类繁多的文件、媒体等资源，浏览器为了区分这些资源，就需要使用MIMEType。所以说**MIME Type是网络资源的媒体类型**。Nginx作为web服务器，也需要能够识别前端请求的资源类型。
在Nginx的配置文件中，默认有两行配置
```js
include mime.types; 
default_type application/octet-stream; 
```
（1）**default_type**:用来配置Nginx响应前端请求默认的MIME类型。

| 语法 | default_type mime-type; |
|:------:|:-----:|
| 默认值 | default_type text/plain； |
| 位置 | http、server、location |

举例来说明：
有些时候请求某些接口的时候需要返回指定的文本字符串或者json字符串，如果逻辑非常简单或者干脆是固定的字符串，那么可以使用nginx快速实现，这样就不用编写程序响应请求了，可以减少服务器资源占用并且响应性能非常快
```js
location /get_text { 
  #这里也可以设置成text/plain 
  default_type text/html; 
  return 200 "This is nginx's text"; 
}
location /get_json { 
  default_type application/json; 
  return 200 '{"name":"TOM","age":18}'; 
}
```

### 自定义服务日志
Nginx中日志的类型分access.log、error.log。
Nginx服务器支持对服务日志的格式、大小、输出等进行设置，需要使用到两个指令，分别是`access_log`和`log_format`指令。
（1）`access_log`:用来设置用户访问日志的相关属性
（2）`log_format`:用来指定日志的输出格式。

### 其他配置指令
（1）`sendfile`:用来设置Nginx服务器是否使用`sendfile()`传输文件，该属性可以大大提高Nginx处理静态资源的性能

| 语法 | sendfile on/off； |
|:------:|:-----:|
| 默认值 | sendfile off; |
| 位置 | http、server、location |

（2）`keepalive_timeout`:用来设置长连接的超时时间
（3）`keepalive_requests`:用来设置一个keep-alive连接使用的次数。

### Server块和location块
**例子**
```js
server { 
  #配置监听端口和主机名称 
  listen 8081; 
  server_name localhost; 
  #配置请求处理日志存放路径 
  access_log /home/www/myweb/server1/logs/access.log server1; 
  #配置错误页面 error_page 404 /404.html; 
  #配置处理/server1/location1请求的location 
  location /server1/location1{ 
    root /home/www/myweb; 
    index index_sr1_location1.html; 
  }
  #配置处理/server1/location2请求的location 
  location /server1/location2{ 
    root /home/www/myweb; index index_sr1_location2.html; 
  }
  #配置错误页面转向 
  location = /404.html { 
    root /home/www/myweb; 
    index 404.html; 
  } 
}
```