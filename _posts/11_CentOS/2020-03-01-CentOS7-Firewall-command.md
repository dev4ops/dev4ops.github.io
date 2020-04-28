---
title: "CentOS7 firewall常用命令 防火墙开启NAT"
subtitle: "CentOS7 firewall常用命令 centos 7 通过firewall限制或开放IP及端口 防火墙开启NAT"
layout: post
author: "dev4ops"
header-style: text
tags:
  - CentOS7
  - firewall
  - 防火墙开启NAT
---
# firewall常用命令

```
#1、重启、关闭、开启firewalld.service服务
service firewalld restart #重启
service firewalld start #开启
service firewalld stop #关闭

#2、查看firewall服务状态
systemctl status firewall 

#3、查看firewall的状态
firewall-cmd --state

#4、查看防火墙规则
firewall-cmd --list-all 


# 开放一个特定端口
firewall-cmd --permanent --zone=public --add-port=22/tcp
firewall-cmd --permanent --zone=public --add-port=80/tcp
firewall-cmd --permanent --zone=public --add-port=443/tcp
firewall-cmd --reload

# 开放一段TCP端口
firewall-cmd --permanent --zone=public --add-port=30000-40000/tcp
firewall-cmd --reload

# 开放所有TCP端口
firewall-cmd --permanent --zone=public --add-port=1-65535/tcp
firewall-cmd --reload

```

# 自定义添加端口
用户可以通过修改配置文件的方式添加端口，也可以通过命令的方式添加端口，注意，修改的内容会在/etc/firewalld/ 目录下的配置文件中还体现。

```
#命令的方式添加端口
firewall-cmd --permanent --add-port=9527/tcp 

#参数介绍：
#1、firewall-cmd：是Linux提供的操作firewall的一个工具；
#2、--permanent：表示设置为持久；
#3、--add-port：标识添加的端口；

#另外，firewall中有Zone的概念，可以将具体的端口制定到具体的zone配置文件中。
#例如：添加8010端口
firewall-cmd --zone=public --permanent --add-port=8010/tcp

#--zone=public：指定的zone为public；
```

# 查询、开放、关闭端口

```
# 查询端口是否开放
firewall-cmd --query-port=8080/tcp
# 开放80端口
firewall-cmd --permanent --add-port=80/tcp
firewall-cmd --permanent --add-port=8080-8085/tcp
# 移除端口
firewall-cmd --permanent --remove-port=8080/tcp
#查看防火墙的开放的端口
firewall-cmd --permanent --list-ports

#重启防火墙(修改配置后要重启防火墙)
firewall-cmd --reload

# 参数解释
#1、firwall-cmd：是Linux提供的操作firewall的一个工具；
#2、--permanent：表示设置为持久；
#3、--add-port：标识添加的端口；
```

# 防火墙开启NAT
参考：
* https://devops.ionos.com/tutorials/deploy-outbound-nat-gateway-on-centos-7/ 
* http://www.chenlianfu.com/?p=2699

```
#传统方法 用下面的方法后可以不用这种方法 -----------------------
#echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.d/ip_forward.conf
#sysctl -w net.ipv4.ip_forward=1
#firewall-cmd --permanent --direct --passthrough ipv4 -t nat -I POSTROUTING -o bond1 -j MASQUERADE -s 192.168.12.0/24
#firewall-cmd --reload
# bond1 为外网网口， 192.168.12.0/24 为 需要转换的内网网段
#  还需要配置域名和其他相关配置

#用下面的方法后可以不用上面的方法-------------------------------------
#第好的方法：
#开启NAT转发
firewall-cmd --permanent --zone=public --add-masquerade
#开放DNS使用的53端口，否则可能导致内网服务器虽然设置正确的DNS，但是依然无法进行域名解析。
firewall-cmd --zone=public --add-port=53/tcp --permanent
#重启防火墙
systemctl restart firewalld.service
# 检查是否允许NAT转发
firewall-cmd --query-masquerade
# 关闭NAT转发
firewall-cmd --remove-masquerade

#第二种方法：
#开启ip_forward转发
#在/etc/sysctl.conf配置文件尾部添加
net.ipv4.ip_forward=1
#然后让其生效
 sysctl -p
#执行firewalld命令进行转发：
firewall-cmd --permanent --direct --passthrough ipv4 -t nat -I POSTROUTING -o enoxxxxxx -j MASQUERADE -s 192.168.1.0/24
#注意enoxxxxxx对应外网网口名称。
#重启防火墙
systemctl restart firewalld.service

```

# centos 7 通过firewall限制或开放IP及端口
http://huanghaiping.com/article/76.html

```
#第一：首先查看防火墙是否开启，如未开启，需要先开启防火墙并作开机自启
systemctl status firewalld  #查看防火墙状态
systemctl start firewalld   #开启防火墙
systemctl stop  firewalld   #禁用防火墙


#第二：开放端口
firewall-cmd --zone=public --add-port=22/tcp --permanent   # 开启22端口
firewall-cmd --zone=public --add-port=80/tcp --permanent   # 开启80端口
firewall-cmd --zone=public --add-port=443/tcp --permanent   # 开启443端口
#其中--permanent的作用是使设置永久生效，不加的话机器重启之后失效

#重新载入一下防火墙设置，使设置生效，每一步操作之后都要重新加载一下防火墙
firewall-cmd --reload
#如下命令可查看当前系统打开的所有端口
firewall-cmd --zone=public --list-ports


#第三：限制端口访问，设置后重新载入一下防火墙设置
firewall-cmd --zone=public --remove-port=22/tcp --permanent  #关闭22端口，其它端口都是同理
firewall-cmd --reload


#第四：批量限制/开放端口的范围
firewall-cmd --zone=public --add-port=100-200/tcp --permanent     #开启100～200的端口访问
firewall-cmd --zone=public --remove-port=100-200/tcp --permanent  #限制100～200的端口访问
firewall-cmd --reload        #重新加载防火墙
firewall-cmd --zone=public --list-ports   #查看端口是否生效


#第五：限制ip地址访问
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0.1" port protocol="tcp" port="80" reject" # 限制IP访问
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0.1" port protocol="tcp" port="80" accept" #解除ip访问
firewall-cmd --reload
firewall-cmd --zone=public --list-rich-rules  #查看规则是否生效


#第六：限制ip段访问
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0/24" port protocol="tcp" port="80" reject" #限制IP段访问
firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="192.168.0/24" port protocol="tcp" port="80" accept" #解除限制
firewall-cmd --reload
firewall-cmd --zone=public --list-rich-rules  #查看规则是否生效
#中192.168.0/24表示为从192.168.0.0这个IP开始，24代表子网掩码为255.255.255.0，共包含256个地址，即从0-255共256个IP，即正好限制了这一整段的IP地址
```




# 限制IP及端口的方式

```
#限制IP地址访问
#方式一：Linux防火墙实现
#阻止所有IP访问
iptables -A INPUT -s 0.0.0.0/0 -p tcp --dport 80 -j DROP
#然后再添加白名单
iptables -A INPUT -s 1.2.3.4 -p tcp --dport 80 -j ACCEPT
###############或者###########
iptables -A INPUT -s 2.3.4.5 -p tcp -j ACCEPT
#方式二：nginx配置实现
##对应的location添加指定规则
location / {
    allow 132.23.22.185;
    deny all;
}

#方式三：/etc/hosts.allow和/etc/hosts.deny
vi /etc/hosts.allow   # 最后一行加入：
# sshd:192.168.0.222:allow //多个IP可以按照此格式写多行
vi /etc/hosts.deny # 最后一行加入：
# sshd:ALL //除了上面允许登录的IP，其它IP都拒绝登录
service sshd restart

vim/etc/ssh/sshd_config # 最后一行加入
# AllowUsers keyso@192.168.0.222 root@192.168.1.135 //多个用户名@IP之间使用空格分隔

```


# CentOS切换为iptables防火墙
切换到iptables首先应该关掉默认的firewalld，然后安装iptables服务。

```
#1、关闭firewall：
service firewalld stop
systemctl disable firewalld.service #禁止firewall开机启动

#2、安装iptables防火墙
yum install iptables-services #安装

#3、编辑iptables防火墙配置
vi /etc/sysconfig/iptables #编辑防火墙配置文件

#下边是一个完整的配置文件：

Firewall configuration written by system-config-firewall
Manual customization of this file is not recommended.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT

#:wq! #保存退出

service iptables start #开启
systemctl enable iptables.service #设置防火墙开机启动
```
