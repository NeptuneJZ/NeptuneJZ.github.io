---
layout: post
title: LEDE 之批量升级软件包 
categories: LEDE, Linux
description: 在LEDE中批量升级软件包。
keywords: LEDE, Linux, update, upgrade
---

在LEDE中使用upgrade不像其他Linux可以直接批量升级软件包，所以需要使用表达式的方法。

`opkg list_installed | sed 's/ - .*//' | sed 's/^/opkg upgrade /'`
`opkg list-upgradable | awk -F ' - ' '{print $1}' | xargs opkg upgrade`

还可以写成脚本
新建一个shell脚本文件，如/root/autoupgrade.sh，加上执行权限，写入以下几行：

```
#!/bin/ash
 
opkg update
for ipk in $(opkg list-upgradable | awk '$1!~/^kmod|^Multiple/{print $1}'); do
	opkg upgrade $ipk
done
```

然后加入到计划任务中：

```
# 文件格式说明
#  ——分钟 (0 - 59)
# |  ——小时 (0 - 23)
# | |  ——日   (1 - 31)
# | | |  ——月   (1 - 12)
# | | | |  ——星期 (0 - 7)（星期日=0或7）
# | | | | |
# * * * * * 被执行的命令
  0 3 * * 6 /root/autoupgrade.sh
```
