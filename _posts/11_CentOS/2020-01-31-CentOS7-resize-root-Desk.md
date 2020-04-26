---
title: "CentOS7 / 根目录扩容"
subtitle: "CentOS7 移除home目录挂载并扩展根目录容量"
layout: post
author: "dev4ops"
header-style: text
tags:
  - CentOS7
  - 根目录扩容
  - home目录移除
---


```

# CentOS7根目录扩容，/home目录缩减 # 谨慎参考https://my.oschina.net/jasonMrliu/blog/1604954 # centos 7 LVM调整 root 和 home 的容量大小
#把/home内容备份，然后将/home文件系统所在的逻辑卷删除，扩大/root文件系统，新建/home：
# tar cvf /tmp/home.tar /home #备份/home
mv /home /mnt/home  # 或者将home目录内容移动到根目录的挂载

lvdisplay |grep home
cat /etc/fstab |grep /home

umount /home #卸载/home，如果无法卸载，先终止使用/home文件系统的进程
lvdisplay # 确定

#lvremove /dev/centos/home #删除/home所在的lv
#lvremove /dev/mapper/cl-home #删除/home所在的lv
lvremove /dev/cl/home  
# fuser -km /home/ # 如果报错可以用这个命令来提掉用户，用户会掉线，有风险，远程操作必须想好退路；

lvdisplay |grep root
#扩展/root所在的lv，增加50G
lvextend -L +500G  /dev/cl/root

lvextend -L +100G  /dev/cl/root

#扩展/root文件系统，增加容量；
xfs_growfs /dev/cl/root 

# 全部挂载到根目录
#lvcreate -L 56G -n home centos #重新创建home lv
#mkfs.xfs /dev/centos/home #创建文件系统
#mount /dev/centos/home /home #挂载
#df -h


mv  /mnt/home/*  /home/ # 将home目录内容移动回来

```