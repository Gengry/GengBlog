---
title: 虚拟机安装Centos，安装mysql，配置主从（一）
date: 2018-02-05 13:20:59
tags:
- mysql
categories:
- linux
- mysql
---

# 前言
公司线上使用阿里云的mysql数据库，一直是单点，也是系统中最大的瓶颈。因为开始考虑的是单点，所以第一个负责人将RDS的配置设置的很高，造成了特别大的资源浪费。平时cpu的内存的使用率都特别低。
现在的负责人觉得RDS高配置很浪费，做了降配处理，春节前活动直接被查询宕机。所以现在需要考虑优化方案
<!--more-->
## 方案一

直接使用阿里云的RDS配置主从，RDS自动支持读写分离，简单易用，能用钱解决的问题都是小问题，暂定也是使用这个方案。

## 方案二

使用阿里云的ECS自己搭建mysql的集群。配置mysql读写分离，高可用，防止单点故障造成直接宕机。
虽然定的方案是使用RDS，但是我还是在本地搭建一下这个环境，以备不时之需。  

### 安装VMware
此步骤略过。

### 安装Centos
下载Centos的ISO包，但是官网的速度简直是巨慢无比无法忍受，可以在中国的镜像站下载。
可以从我的博客[国内开源镜像站](/2018/01/29/国内开源镜像站/)获取开源镜像站
推荐使用[中国科学技术大学](http://mirrors.ustc.edu.cn/)。
页面中可以`Ctrl+f`搜索centos快速定位。在文件目录下找到centos官方推荐使用的最新版本。[CentOS7](http://mirrors.ustc.edu.cn/centos/7.4.1708/isos/x86_64/CentOS-7-x86_64-DVD-1708.iso)

> CentOS-7-x86_64-DVD-1708.iso               标准安装版，一般下载这个就可以了  
> CentOS-7-x86_64-NetInstall-1708.iso        网络安装镜像  
> CentOS-7-x86_64-Everything-1708.iso        对完整版安装盘的软件进行补充，集成所有软件。  
> CentOS-7-x86_64-LiveGNOME-1708.iso     GNOME桌面版  
> CentOS-7-x86_64-LiveKDE-1708.iso           KDE桌面版  
> CCentOS-7-x86_64-LiveKDE-1708-livecd.iso            光盘上运行的系统，类拟于winpe   

#### 安装

![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/1.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/2.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/3.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/4.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/5.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/6.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/7.jpg)  
新手最简单安装一路继续，安装成功后登陆。  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/8.jpg)  
配置网络，在安装完虚拟就后一般会自动生成两个网络适配器，这个可以删除也可以自定义，但是本次我们使用默认生成的`VMware Network Adapter VMnet8`  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/9.jpg)  
现在需要将可以上网的网卡的网络共享给虚拟机的网络适配器。  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/10.jpg)  
现在看VMware Network Adapter VMnet8的属性，可以看到为他生成了ip地址。  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/11.jpg)  
根据这个信息配置，虚拟机的虚拟网络（编辑->虚拟网络编辑器）  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/12.jpg)  

#### 配置

虚拟机启动默认网络是不自动开启的，编辑网络的配置文件  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/13.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/14.jpg)  
重启network服务，已经获取到ip地址，测试实体机可以ping通。  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/15.jpg)  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/16.jpg)  
开启ssh外网连接。  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/17.jpg)  
重启centos。  
使用`xshell`连接。  
![](https://raw.githubusercontent.com/Gengry/blogImage/master/20180205/18.jpg)  


> http://blog.csdn.net/bao19901210/article/details/51917641  
> http://blog.csdn.net/u012961566/article/details/70237548  
> http://blog.csdn.net/djcode/article/details/78621772  


<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>