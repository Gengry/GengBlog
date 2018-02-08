---
title: nginx
tags:
---


## 反向代理
upstream my_server_pool{
	server 192.168.10.1 weight=1 max_fails=2 fail_timeout=30s #最大失败次数2，30s内失败两次切换到其他server
	server 192.168.10.2 weight=1 max_fails=2 fail_timeout=30s
}

upstream bbs_server_pool{
	server 192.168.10.3 weight=1 max_fails=2 fail_timeout=30s #最大失败次数2，30s内失败两次切换到其他server
	server 192.168.10.4 weight=1 max_fails=2 fail_timeout=30s
}

location /message { #将message的请求丢到my_server_pool 属于应用层的负载均衡
	proxy_pass http://my_server_pool;
	proxy_set_header Host www.abc.com;
	proxy_set_header X-Forward-For $remote_addr;
}

location /bbs { 
	proxy_pass http://bbs_server_pool;
	proxy_set_header Host www.abc.com;
	proxy_set_header X-Forward-For $remote_addr;
}

## 地址重写

请求连接方法
select poll kqueue epoll
apache是select模型
nginx是epoll模型

## IO
网络IO分为两种，阻塞IO，多路复用IO

## nginx编译和安装
yum -y install gcc gcc-c++ autoconf automake
yum -y install zlib zlib-devel openssl openssl-devel pcre-devel

zlib :nginx 提供zip
openssl ： ssl功能
pcre： 提供rewrite功能（地址重写）

### 为nginx配置用户
```
groupadd -r nginx   # -r 指定创建系统用户
useradd -s /sbin/nologin -g nginx -r nginx

```

tar -zxvf nginx-tar.gz
cd nginx
./configure --help 可以查看帮助 里面的参数介绍例如 安装路径，bin sbin 配置文件，日志，pid文件路径（如果不指定就是按照linux默认的）
 设置启动用户  安装的模块等
					
直接 ./configure回车 按默认配置安装   
想指定参数在 ./configure 后加 \ 回车，可以换行继续输入   
```

# ./configure \
> --prefix=...

```

默认安装 各个文件位置

```
nginx path prefix: "/usr/local/nginx"
nginx binary file: "/usr/local/nginx/sbin/nginx"
nginx modules path: "/usr/local/nginx/modules"
nginx configuration prefix: "/usr/local/nginx/conf"
nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
nginx pid file: "/usr/local/nginx/logs/nginx.pid"
nginx error log file: "/usr/local/nginx/logs/error.log"
nginx http access log file: "/usr/local/nginx/logs/access.log"
nginx http client request body temporary files: "client_body_temp"
nginx http proxy temporary files: "proxy_temp"
nginx http fastcgi temporary files: "fastcgi_temp"
nginx http uwsgi temporary files: "uwsgi_temp"
nginx http scgi temporary files: "scgi_temp"
```



make && make install


## 启动

./nginx -c /usr/local/nginx/conf/nginx.conf
ps -ef | grep nginx 
netstat -tulnp | grep 80 
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      3791/nginx: master  
./nginx -s quit

> nginx.conf 配置文件中指定user 如果不指定user默认是nobody 运行 worker process，可能会造成没有权限问题

源码安装后可以从其他用rpm安装的电脑拷贝/etc/init.d/nginx的脚本 chmod添加权限x  还有服务配置文件中几个文件路径可能不对 需要修改成对应的。  
使用 chkconfig --add nginx安装这个
chkconfig --list nginx

这个命令会在/etc/rc.d 中创建连接文件

## 配置
nginx建议有几个核配置nginx几个进程

需要配置防火墙，否则外网访问不了

