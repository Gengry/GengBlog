---
title: 虚拟机安装Centos，安装mysql，配置主从（二）
date: 2018-02-06 12:59:41
tags:
categories:
- linux
- mysql
---

# 下载mysql
仍然使用[中国科学技术大学](http://mirrors.ustc.edu.cn/)镜像站下载mysql。  
最新版本的[mysql](http://iso.mirrors.ustc.edu.cn/mysql-ftp/Downloads/MySQL-5.7/mysql-5.7.20-linux-glibc2.12-i686.tar)。
这里下载的mysql是二进制包，没有使用linux的包管理工具。

# 阅读mysql安装文档
通过百度打开mysql的官网，找到mysql5.7GA的文档。  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/19.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/20.jpg)  

``` shell
shell> groupadd mysql
shell> useradd -r -g mysql -s /bin/false mysql
shell> cd /usr/local
shell> tar zxvf /path/to/mysql-VERSION-OS.tar.gz
shell> ln -s full-path-to-mysql-VERSION-OS mysql
shell> cd mysql
shell> mkdir mysql-files
shell> chown mysql:mysql mysql-files
shell> chmod 750 mysql-files
shell> bin/mysqld --initialize --user=mysql 
shell> bin/mysql_ssl_rsa_setup              
shell> bin/mysqld_safe --user=mysql &
# Next command is optional
shell> cp support-files/mysql.server /etc/init.d/mysql.server
```

# 安装
使用xshell工具通过ssh连接到centos，ssh协议支持xftp协议，可以将本地的文件上传到虚拟机中的centos中。
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/21.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/22.jpg)  

## 创建用户及相关

![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/23.jpg)  
将mysql目录及其子目录的所有者设置为mysql

``` shell
mv mysql-5.7.21-linux-glibc2.12-x86_64 mysql
cd /usr/local
ln -s /home/mysql/mysql mysql
cd /home/mysql
mkdir mysql-data
mkdir mysql-logs
mkdir mysql-tmp
chown mysql:mysql -R mysql/
# 初始化mysql的data文件夹
[root@localhost mysql]# ./bin/mysqld  --initialize --user=mysql --basedir=/home/mysql/mysql --datadir=/home/mysql/mysql-data
```
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/24.jpg)  

## mysql配置文件

将下面的配置复制到 /etc/my.cnf中
```
[client]
default_character_set = utf8
port = 3306
#这个路径需要和mysqld中socket的路径相同
socket = /tmp/mysql.sock

[mysqld]
basedir = /home/mysql/mysql
datadir = /home/mysql/mysql-data
user = mysql
port = 3306
server_id = 1
character_set_server = utf8
socket = /tmp/mysql.sock
pid-file = /tmp/mysql.pid
log-bin = /home/mysql/mysql-logs/bin_log  # 二进制日志，用户主从复制
relay-log = /home/mysql/mysql-logs/relay_log  #从mysql的IO thread将主的bin_log复制到这个文件
log-error = /home/mysql/mysql-logs/mysql_error.log
explicit_defaults_for_timestamp = true
expire_logs_days = 10
max_binlog_size = 100M
binlog-do-db =    #复制数据库名称
binlog-ignore-db = mysql #忽略二进制日志的数据库名
```

## 配置启动脚本

```shell
shell> cp support-files/mysql.server /etc/init.d/mysql.server
```

## 启动mysql

```shell
service mysql start
```
报错
`MySQL.2018-02-06T06:07:04.427707Z mysqld_safe error: log-error set to '/home/mysql/mysql-logs/mysql_error.log', however file don't exists. Create writable for user 'mysql'`

需要手动建立error_log文件
```
touch /home/mysql/mysql-logs/mysql_error.log
service mysql start
```

mysql已正常启动，可以使用mysql/bin目录下的mysql和之前记录的默认密码登录mysql
为root账户设置密码
```
alter user 'root'@'localhost' identified by 'root';
```

把mysql注册成开机启动的服务
```
chkconfig --add mysql  
```
## 加入环境变量
修改/etc/profile文件使其永久性生效，并对所有系统用户生效，在文件末尾加上如下两行代码
PATH=$PATH:/home/mysql/mysql:/home/mysql/mysql/bin
export PATH
最后：执行 命令source /etc/profile或 执行点命令 ./profile使其修改生效，执行完可通过echo $PATH命令查看是否添加成功。

## 配置账户
登录mysql配置一个可以远程访问的账户
```sql
GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%'IDENTIFIED BY 'myuser' WITH GRANT OPTION;
FLUSH PRIVILEGES 
```

暂时关闭防火墙，否则外面访问不了
systemctl stop firewalld.service

# 使用Navicat连接mysql
使用账户myuser 密码 myuser登录，
第一次登录没有成功，在本地用myuser登录一下mysql就可以用navicat连接上了。
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/25.jpg)  

<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>