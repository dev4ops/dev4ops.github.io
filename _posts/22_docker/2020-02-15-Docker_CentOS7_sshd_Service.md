---
title: "Docker CentOS7 sshd+crond service"
subtitle: "Docker的CentOS7镜像生成sshd+crontab服务"
layout: post
author: "dev4ops"
header-style: text
tags:
  - docker
  - service
  - sshd+crontab(crond)
  - CentOS7
---

# 命令脚本
```
mkdir -p  /home/bk/data
mkdir -p  /home/bk/opt

# docker 创建一个静态IP的网络
docker network create --subnet=172.20.20.0/24 static_ip
# 删除
docker stop  bk
docker rm  bk

# docker 创建一个
docker run -itd \
  --restart=always \
  -e "container=docker" --privileged=true \
  --hostname bk \
  --name bk \
  --security-opt seccomp:unconfined --cap-add=SYS_ADMIN -v /sys/fs/cgroup:/sys/fs/cgroup:ro \
  -v /home/bk/data:/data \
  -v /home/bk/opt:/opt \
  --mac-address 02:42:ac:11:00:01 \
  --network static_ip \
  --ip 172.20.20.10 \
  centos:7 bash -c "/usr/sbin/init"

# 有service的容器，参考如下：
docker run -it -p 80:80 -e "container=docker" --privileged=true -d --security-opt seccomp:unconfined --cap-add=SYS_ADMIN -v /sys/fs/cgroup:/sys/fs/cgroup:ro my-centos:7 bash -c "/usr/sbin/init"

#  -p 8088:80 -p 8443:443 \

# 进入容器：
docker exec -it bk /bin/bash

# 安装常规应用
yum -y install epel-release
yum clean all
yum makecache
yum update -y
yum install -y libyaml-devel libffi-developenssl-devel make bzip2 autoconf automake
yum install -y libtool bison curl sqlite-devel telnet vim links
yum install -y initscripts net-tools wget htop tree
yum install -y rsyslog
systemctl enable rsyslog.service
systemctl enable sshd.service

# 安装网络等常规命令
yum install -y net-tools wget unzip  traceroute bind-utils  nload ifstat iftop
# 安装SSH+Crond服务
yum install -y rsync crontabs passwd openssl openssh-server openssh-clients   which  initscripts 

# 启动服务
systemctl enable sshd && systemctl restart  sshd && systemctl status sshd 
systemctl enable crond && systemctl restart  crond && systemctl status crond 


```
