---
title: "Nginx安装及注意事项"
subtitle: "在CentOS7下安装Nginx需要注意的事项"
layout: post
author: "dev4ops"
header-style: text
tags:
  - nginx
  - install
  - 注意事项
---

# Install Nginx on CentOS 7
参考信息：
* https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-centos-7


```shell script
yum install epel-release -y
yum install nginx -y

systemctl enable nginx && systemctl restart nginx && systemctl status nginx

systemctl restart nginx && systemctl status nginx


yum -y install firewalld
 /bin/systemctl restart firewalld.service &&  /bin/systemctl enable firewalld.service  && /bin/systemctl status firewalld.service

firewall-cmd --permanent --zone=public --add-service=http 
firewall-cmd --permanent --zone=public --add-service=https
firewall-cmd --reload

# 强杀Nginx
ps -ef |grep nginx |grep -v grep  |awk '{print $2}' |xargs kill -9


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

```


# nginx port range
```shell script

#Port ranges (1.15.10) are specified with the first and last port separated by a hyphen:  http://nginx.org/en/docs/stream/ngx_stream_core_module.html#listen

listen 127.0.0.1:12345-12399;
listen 12345-12399;

proxy_pass backend-host-name:$server_port;
#$server_port

```

