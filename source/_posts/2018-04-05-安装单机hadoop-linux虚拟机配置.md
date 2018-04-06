---
title: 安装单机hadoop-linux虚拟机配置
date: 2018-04-05 11:38:36
tags:
categories:
---

伪分布模式安装步骤
* 关闭防火墙
* 修改ip
* 修改hostname
* 设置ssh自动登录
* 安装jdk
* 安装hadoop
<!-- more -->

## 防火墙

CentOS 6.5关闭防火墙
[root@localhost ~]#servcie iptables stop                    --临时关闭防火墙
[root@localhost ~]#chkconfig iptables off                    --永久关闭防火墙

CentOS 7.2关闭防火墙
CentOS 7.0默认使用的是firewall作为防火墙，这里改为iptables防火墙步骤。
firewall-cmd --state #查看默认防火墙状态（关闭后显示notrunning，开启后显示running）
[root@localhost ~]#firewall-cmd --state
not running

systemctl stop firewalld.service #停止firewall
systemctl disable firewalld.service #禁止firewall开机启动

## 修改ip

> vim /etc/sysconfig/network-script/ifcfg-eth0
BOOTPROTO=static
IPADDR=192.168.137.3
NETMASK=255.255.255.0
GATEWAY=192.168.137.1
DNS1=8.8.8.8
DNS2=8.8.4.4



## 安装jdk

下载linux版的jdk，使用xshell上传到虚拟机。

>   tar -zxvf jdk-7u80-linux-x64.tar.gz
>   mv jdk1.7.0_80/ /usr/local/
>   vim /etc/profile
> export JAVA_HOME=/usr/local/jdk1.7.0_80　　
  export JRE_HOME=/usr/local/jdk1.7.0_80/jre 
　export PATH=$PATH:/usr/local/jdk1.7.0_80/bin 
　export CLASSPATH=./:/usr/local/jdk1.7.0_80/lib:/usr/local/jdk1.7.0_80/jre/lib



1.准备Linux环境
	1.0点击VMware快捷方式，右键打开文件所在位置 -> 双击vmnetcfg.exe -> VMnet1 host-only ->修改subnet ip 设置网段：192.168.1.0 子网掩码：255.255.255.0 -> apply -> ok
		回到windows --> 打开网络和共享中心 -> 更改适配器设置 -> 右键VMnet1 -> 属性 -> 双击IPv4 -> 设置windows的IP：192.168.1.110 子网掩码：255.255.255.0 -> 点击确定
		在虚拟软件上 --My Computer -> 选中虚拟机 -> 右键 -> settings -> network adapter -> host only -> ok	
	1.1修改主机名
		vim /etc/sysconfig/network
		
		NETWORKING=yes 
		HOSTNAME=itcast01    ###

	1.2修改IP
		两种方式：
		第一种：通过Linux图形界面进行修改（强烈推荐）
			进入Linux图形界面 -> 右键点击右上方的两个小电脑 -> 点击Edit connections -> 选中当前网络System eth0 -> 点击edit按钮 -> 选择IPv4 -> method选择为manual -> 点击add按钮 -> 添加IP：192.168.1.119 子网掩码：255.255.255.0 网关：192.168.1.1 -> apply
	
		第二种：修改配置文件方式（屌丝程序猿专用）
			vim /etc/sysconfig/network-scripts/ifcfg-eth0
			
			DEVICE="eth0"
			BOOTPROTO="static"           ###
			HWADDR="00:0C:29:3C:BF:E7"
			IPV6INIT="yes"
			NM_CONTROLLED="yes"
			ONBOOT="yes"
			TYPE="Ethernet"
			UUID="ce22eeca-ecde-4536-8cc2-ef0dc36d4a8c"
			IPADDR="192.168.1.44"       ###
			NETMASK="255.255.255.0"      ###
			GATEWAY="192.168.1.1"        ###
			
	1.3修改主机名和IP的映射关系
		vim /etc/hosts
			
		192.168.1.44	itcast01
	
	1.4关闭防火墙
		#查看防火墙状态
		service iptables status
		#关闭防火墙
		service iptables stop
		#查看防火墙开机启动状态
		chkconfig iptables --list
		#关闭防火墙开机启动
		chkconfig iptables off
	
	1.5重启Linux
		reboot
	
2.安装JDK
	2.1上传
	
	2.2解压jdk
		#创建文件夹
		mkdir /usr/java
		#解压
		tar -zxvf jdk-7u55-linux-i586.tar.gz -C /usr/java/
		
	2.3将java添加到环境变量中
		vim /etc/profile
		#在文件最后添加
		export JAVA_HOME=/usr/java/jdk1.7.0_55
		export PATH=$PATH:$JAVA_HOME/bin
	
		#刷新配置
		source /etc/profile
3.安装Hadoop
	3.1上传hadoop安装包
	
	3.2解压hadoop安装包
		mkdir /cloud
		#解压到/cloud/目录下
		tar -zxvf hadoop-2.2.0.tar.gz -C /cloud/
		
	3.3修改配置文件（5个）
		第一个：hadoop-env.sh
		#在27行修改
		export JAVA_HOME=/usr/java/jdk1.7.0_55
		
		第二个：core-site.xml
		<configuration>
			<!-- 指定HDFS老大（namenode）的通信地址 -->
			<property>
					<name>fs.defaultFS</name>
					<value>hdfs://itcast01:9000</value>
			</property>
			<!-- 指定hadoop运行时产生文件的存储路径 -->
			<property>
					<name>hadoop.tmp.dir</name>
					<value>/cloud/hadoop-2.2.0/tmp</value>
			</property>
		</configuration>
		
		第三个：hdfs-site.xml
		<configuration>
			<!-- 设置hdfs副本数量 -->
			<property>
					<name>dfs.replication</name>
					<value>1</value>
			</property>
		</configuration>
		
		第四个：mapred-site.xml.template 需要重命名： mv mapred-site.xml.template mapred-site.xml
		<configuration>
			<!-- 通知框架MR使用YARN -->
			<property>
					<name>mapreduce.framework.name</name>
					<value>yarn</value>
			</property>
		</configuration>
		
		第五个：yarn-site.xml
		<configuration>
			<!-- reducer取数据的方式是mapreduce_shuffle -->
			<property>
				<name>yarn.nodemanager.aux-services</name>
				<value>mapreduce_shuffle</value>
			</property>
		</configuration>
	
	3.4将hadoop添加到环境变量
		vim /etc/profile
		
		export JAVA_HOME=/usr/java/jdk1.7.0_55
		export HADOOP_HOME=/cloud/hadoop-2.2.0
		export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin
	
		source /etc/profile
	3.5格式化HDFS（namenode）第一次使用时要格式化
		hadoop namenode -format
		
	3.6启动hadoop
		先启动HDFS
		sbin/start-dfs.sh
		
		再启动YARN
		sbin/start-yarn.sh
		
	3.7验证是否启动成功
		使用jps命令验证
		27408 NameNode
		28218 Jps
		27643 SecondaryNameNode
		28066 NodeManager
		27803 ResourceManager
		27512 DataNode
	
		http://192.168.1.44:50070  (HDFS管理界面)
		在这个文件中添加linux主机名和IP的映射关系
		C:\Windows\System32\drivers\etc\hosts
		192.168.1.119	itcast
		
		http://192.168.1.44:8088 （MR管理界面）
		
4.配置ssh免登陆
	生成ssh免登陆密钥
	cd ~，进入到我的home目录
	cd .ssh/

	ssh-keygen -t rsa （四个回车）
	执行完这个命令后，会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）
	将公钥拷贝到要免登陆的机器上
	cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
	或
	ssh-copy-id -i localhost 

5.hdfs基本使用

	1.0查看帮助
		hadoop fs -help <cmd>
	1.1上传
		hadoop fs -put <linux上文件> <hdfs上的路径>
	1.2查看文件内容
		hadoop fs -cat <hdfs上的路径>
	1.3查看文件列表
		hadoop fs -ls /
	1.4下载文件
		hadoop fs -get <hdfs上的路径> <linux上文件>

6.MapReduce示例
	
hadoop为我们提供了mr的示例程序，存放在/hadoop/hadoop-2.2.0/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar
	
		
安装hadoop2.4.1
	注意：hadoop2.x的配置文件$HADOOP_HOME/etc/hadoop
	伪分布式需要修改5个配置文件
	3.1配置hadoop
	第一个：hadoop-env.sh
		vim hadoop-env.sh
		#第27行
		export JAVA_HOME=/usr/java/jdk1.7.0_65
		
	第二个：core-site.xml
		<!-- 制定HDFS的老大（NameNode）的地址 -->
		<property>
			<name>fs.defaultFS</name>
			<value>hdfs://itcast01:9000</value>
		</property>
		<!-- 指定hadoop运行时产生文件的存储目录 -->
		<property>
			<name>hadoop.tmp.dir</name>
			<value>/itcast/hadoop-2.4.1/tmp</value>
        </property>
		
	第三个：hdfs-site.xml
		<!-- 指定HDFS副本的数量 -->
		<property>
			<name>dfs.replication</name>
			<value>1</value>
        </property>
		
	第四个：mapred-site.xml (mv mapred-site.xml.template mapred-site.xml)
		mv mapred-site.xml.template mapred-site.xml
		vim mapred-site.xml
		<!-- 指定mr运行在yarn上 -->
		<property>
			<name>mapreduce.framework.name</name>
			<value>yarn</value>
        </property>
		
	第五个：yarn-site.xml
		<!-- 指定YARN的老大（ResourceManager）的地址 -->
		<property>
			<name>yarn.resourcemanager.hostname</name>
			<value>itcast01</value>
        </property>
		<!-- reducer获取数据的方式 -->
        <property>
			<name>yarn.nodemanager.aux-services</name>
			<value>mapreduce_shuffle</value>
        </property>
	
	3.2将hadoop添加到环境变量
	
	vim /etc/proflie
		export JAVA_HOME=/usr/java/jdk1.7.0_65
		export HADOOP_HOME=/itcast/hadoop-2.4.1
		export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

	source /etc/profile
	
	3.3格式化namenode（是对namenode进行初始化）
		hdfs namenode -format (hadoop namenode -format)
		
	3.4启动hadoop
		先启动HDFS
		sbin/start-dfs.sh
		
		再启动YARN
		sbin/start-yarn.sh
		
	3.5验证是否启动成功
		使用jps命令验证
		27408 NameNode
		28218 Jps
		27643 SecondaryNameNode
		28066 NodeManager
		27803 ResourceManager
		27512 DataNode
	
		http://192.168.8.118:50070 （HDFS管理界面）
		http://192.168.8.118:8088 （MR管理界面）
	
## 遇到的问题

启动hdfs，datanode无法启动。
可以看到datanode日志文件报错`hadoop-root-datanode-localhost.localdomain.log`
`org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.protocol.DisallowedDatanodeException): Datanode denied communication with namenode: DatanodeRegistration(0.0.0.0, storageID=DS-1492551125-127.0.0.1-50010-1522922793693, infoPort=50075, ipcPort=50020, storageInfo=lv=-47;cid=CID-0a985653-165f-4fed-8366-d5552fe4abd9;nsid=150074941;c=0)
at org.apache.hadoop.hdfs.server.blockmanagement.DatanodeManager.registerDatanode(DatanodeManager.java:739)`

解决：
	修改hosts文件  vim /etc/hosts
	添加 192.168.138.3 localhost
解决办法二：
	在namenode的hdfs-site.xml 里面添加
	```
		<property>
			<name>dfs.namenode.datanode.registration.ip-hostname-check</name>
			<value>false</value>
		</property>
	```
网上给出的错误原因：
错误和原来的hosts中的“127.0.0.1 localhost”没有关系，是增加了的“192.168.1.101 localhost”解决了问题。原因在于RSA没有给局域网ip（192.168.1.101）授权，造成data node没有正常启动。
	
项目启动后http://192.168.137.3:50070页面内的`Browse the filesystem`无法访问

解决：
	点击`Browse the filesystem`浏览器中的地址栏为`http://localhost:50075/browseDirectory.jsp?namenodeInfoPort=50070&dir=/&nnaddr=192.168.137.3:9000`
	很明显是地址不对，但是用鼠标指着`Browse the filesystem`显示的地址是对的，说明在browseDirectory.jsp做了一次页面跳转。
	将地址手动修改为192.168.137.3:50075后可以正常访问。
	猜测：
		`browseDirectory.jsp`这个页面内namenode随机选择了一个datanode的地址做跳转，对于namenode来说，单机模式的datanode地址就是localhost。
		我自己配置的时候并没有配置主机名，主机默认就是localhost。
	修改：
		配置主机名
		vim /etc/sysconfig/network
		NETWORKING=yes 
		HOSTNAME=gengry    ###
		配置主机名和ip对应关系
		vim /etc/hosts
		192.168.137.3	gengry
		配置windows的hosts
		192.168.137.3	gengry
	重启hadoop，再次访问，正常。
	
<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>