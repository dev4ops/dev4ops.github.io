---
title: "CentOS7系统初始化"
subtitle: "CentOS7初始化系统的常规命令"
layout: post
author: "dev4ops"
header-style: text
hidden: true
tags:
  - CentOS7
  - 初始化
  - init
---

## 安装常用工具
```shell script

yum update -y
yum install -y net-tools wget unzip  traceroute bind-utils

# 安装网络工具
yum install -y epel-release && yum install -y nload ifstat iftop

yum install -y net-tools wget unzip
yum install -y bind-utils
yum install traceroute -y
```

## 禁用交换分区、selinux 等
```shell script

# 禁用 selinux
BBC=$(getenforce)
if [ "${BBC}" != "Disabled" ] ; then
  echo "disable selinux"
  setenforce 0 > /dev/null 2>&1
  sed -i 's/^SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
  sed -i 's/^SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
  sed -i 's/^SELINUX=permissive/SELINUX=disabled/g' /etc/sysconfig/selinux
  sed -i 's/^SELINUX=permissive/SELINUX=disabled/g' /etc/selinux/config
fi


# -------------------------------

# 禁用交换分区
#swapoff -a && sed -i 's/.*swap.*/#&/' /etc/fstab
swapoff -a && sed -i 's/^\/.*swap.*/#&/' /etc/fstab

# 禁用防火墙
/bin/systemctl status firewalld.service > /dev/null 2>&1
if [ $? -eq 0 ] ; then
  echo "systemctl disable firewalld && systemctl stop firewalld"
  /bin/systemctl disable firewalld && /bin/systemctl stop firewalld
fi

# 添加114 域名解析
a=$(cat /etc/resolv.conf |grep '114.114.114'  |wc -l )
if [ ${a} -eq 0 ] ; then
  echo "Add nameserver 114.114.114.114 to /etc/resolv.conf"
  echo nameserver 114.114.114.114>>/etc/resolv.conf
fi

echo "base system OK!"
```

