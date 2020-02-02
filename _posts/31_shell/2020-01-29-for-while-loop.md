---
title: "shell循环"
subtitle: "循环数组、循环读取目录或文件、for循环和while循环"
layout: post
author: "dev4ops"
header-style: text
hidden: true
tags:
  - shell
  - 循环
  - 数组
  - 编码
---


##shell 数组循环
```shell script
#首先创建一个数组 
array=( A B C D 1 2 3 4)
###1.标准的for循环
for(( i=0;i<${#array[@]};i++)) do
#${#array[@]}获取数组长度用于循环
echo ${array[i]};
done;
###2.for … in 遍历（不带数组下标）：
for element in ${array[@]}
#也可以写成for element in ${array[*]}
do
echo $element
done

#遍历（带数组下标）：
for i in "${!arr[@]}";   
do   
    printf "%s\t%s\n" "$i" "${arr[$i]}"  
done  

###3.While循环法：
i=0  
while [ $i -lt ${#array[@]} ]  
#当变量（下标）小于数组长度时进入循环体
do  
    echo ${ array[$i] }  
    #按下标打印数组元素
    let i++  
done  

```

# linux shell 按行循环读入文件方法
```shell script


#!/bin/bash
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

while read line
do
 echo $line
done < filename(待读取的文件)

cat filename(待读取的文件) | while read line
do
 echo $line
done

for line in `cat filename(待读取的文件)`
do
 echo $line
done


#!/usr/bin/env bash
> res.txt
for line in $(cat aa.txt);do
    A=$(echo $line|awk -F, '{print $1}')
    B=$(echo $line|awk -F, '{print $2}')
    echo $A - $B
    echo 'curl -X POST http://127.0.0.1:10009/ngService/openOne -H Request-Origion:SwaggerBootstrapUi -H accept:*/* -H Content-Type:application/json -d {"areaCode":"047","associaPhone":[],"cfuMsidn":"","eparchyCode":"'${A}'","id":0,"linkPhone":"","mianPhone":"","msisdn":"'${B}'","old_msisdn":"","opRetCode":"","opRetDesc":"","operType":"","password":"123456","provinceCode":"36","servInstID":"","startDate":"","status":"","subId":"","vicePhone":""}' >> res.txt
done

```

#shell 遍历目录
```shell script

#!/bin/bash
function getdir(){
    for element in `ls $1`
    do  
        dir_or_file=$1"/"$element
        if [ -d $dir_or_file ]
        then 
            getdir $dir_or_file
        else
            echo $dir_or_file
        fi  
    done
}
root_dir="$1"
getdir $root_dir

# ------------

#! /bin/bash
function read_dir(){
for file in `ls $1` #注意此处这是两个反引号，表示运行系统命令
do
 if [ -d $1"/"$file ] #注意此处之间一定要加上空格，否则会报错
 then
 read_dir $1"/"$file
 else
 echo $1"/"$file #在此处处理文件即可
 fi
done
} 
#读取第一个参数
read_dir $1


```

