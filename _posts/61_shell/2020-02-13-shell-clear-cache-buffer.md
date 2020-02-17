---
title: "Linux释放缓存"
subtitle: "Linux释放内存缓存，shell清理内存"
layout: post
author: "dev4ops"
header-style: text
tags:
  - SSH
  - Xshell
  - 隧道
  - tunnel
---

```
sync && echo 1 > /proc/sys/vm/drop_caches
sync && echo 2 > /proc/sys/vm/drop_caches
sync && echo 3 > /proc/sys/vm/drop_caches

```

# How to Clear Cache in Linux?
Every Linux System has three options to clear cache without interrupting any processes or services.
1. Clear PageCache only.清除页缓存
`sync; echo 1 > /proc/sys/vm/drop_caches`
2. Clear dentries and inodes.
`sync; echo 2 > /proc/sys/vm/drop_caches`
3. Clear PageCache, dentries and inodes. 清理所有缓存包括页缓存、索引节点等
`sync; echo 3 > /proc/sys/vm/drop_caches `
Explanation of above command.


# How to Clear Swap Space in Linux?清理交换分区
If you want to clear Swap space, you may like to run the below command. 清理交换分区命令
`swapoff -a && swapon -a`
Also you may add above command to a cron script above, after understanding all the associated risk.
# 一行清理所有内容
Now we will be combining both above commands into one single command to make a proper script to clear RAM Cache and Swap Space.
`echo 3 > /proc/sys/vm/drop_caches && swapoff -a && swapon -a && printf '\n%s\n' 'Ram-cache and Swap Cleared'`
OR
`su -c "echo 3 >'/proc/sys/vm/drop_caches' && swapoff -a && swapon -a && printf '\n%s\n' 'Ram-cache and Swap Cleared'" root`
After testing both above command, we will run command “free -h” before and after running the script and will check cache.

## 参考：
* https://www.tecmint.com/clear-ram-memory-cache-buffer-and-swap-space-on-linux/