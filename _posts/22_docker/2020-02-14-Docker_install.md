---
title: "Docker部署，Docker安装及常规配置，dockercompose安装"
subtitle: "Docker在CentOS下安装"
layout: post
author: "dev4ops"
header-style: text
tags:
  - docker
  - 安装
  - docker compose
  - CentOS7
---


# docker部署
docker参考阿里云Docker CE安装文档：https://yq.aliyun.com/articles/110806
## docker 安装
```
# step 1: 安装必要的一些系统工具
yum install -y yum-utils device-mapper-persistent-data lvm2
# Step 2: 添加软件源信息
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# Step 3: 更新并安装 Docker-CE
yum makecache fast
yum -y install docker-ce

mkdir -p /etc/docker
# Step 4:配置镜像加速器及日志大小限制，避免服务器日志打爆,日志保存到本地，版本
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://fy156yq5.mirror.aliyuncs.com"],
  "log-driver":"json-file",
  "log-opts": {"max-size":"500m", "max-file":"3"},
  "insecure-registries": [
    "reg.cuopen.net", 
    "reg.cuopen.net:80", 
    "reg.cuopen.net:5000", 
    "harbor.cuopen.net", 
    "harbor.cuopen.net:80", 
    "harbor.cuopen.net:5000", 
    "reg.harbor", 
    "reg.harbor:80", 
    "reg.harbor:5000"
  ]
}
EOF

cat /etc/docker/daemon.json
systemctl daemon-reload


# Step 5: 开启Docker服务
systemctl daemon-reload && systemctl enable docker && systemctl restart docker && systemctl status docker

# 安装校验
docker version

```

## dockercompose安装
```
yum update -y

yum install -y epel-release
yum install -y python-pip
pip install --upgrade pip
pip install docker-compose
yum upgrade python* -y
docker-compose version


#如果上面安装失败，可以直接下载docker-compose命令到对应目录即可
#https://github.com/docker/compose/releases

curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
ln -s /usr/local/bin/docker-compose /bin/docker-compose
docker-compose --version


```

## 安装异常尝试
```
# 使用官方安装脚本自动安装 （仅适用于公网环境）
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
# 此步凑仅安装Docker，还需要对Docker设置和优化。从上面的step4开始
```
## 日志保存到fluentd,修改对应的FLUENTD_ADD地址,EFK服务器本身不能使用此配置
```

FLUENTD_ADD="192.168.1.114:24224"
tee /etc/docker/daemon.json <<-EOF
{
  "registry-mirrors": ["https://fy156yq5.mirror.aliyuncs.com"],
  "log-driver": "fluentd",
  "log-opts": { "fluentd-address": "${FLUENTD_ADD}" },
  "insecure-registries": [
    "reg.harbor", 
    "reg.harbor:80", 
    "reg.harbor:5000"
  ]
}
EOF

```

# docker 清理所有东西
```
docker ps -a
docker rm -f $(docker ps -aq)
yes| docker container prune
yes |docker volume prune
docker ps -a

reboot
```