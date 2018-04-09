---
title: vultr搭建ssr服务器及bbr加速教程
date: 2018-03-22 22:30:17
tags: [ssr]
categories: 网络
---

科学上网的方式很多，最稳定安全可靠的当属自建vps服务器。这里记录自己搭建vps的全过程，以供参考。

<!-- more -->

## vps商家选择

首先，有信用卡的同学，可以选择谷歌云或亚马逊aws，绑定信用卡都可以免费使用一年，配置高，速度杠杠的。可以自行搜索相关教程。

> vps和云服务器的区别：
>
> VPS的虚拟机是一台物理服务器虚拟出的多个独立服务器，云服务器是运行在一个物理服务器集群上的。云服务器可以动态平衡负载，弹性调整配置，而且稳定性更高。

没信用卡就只能其他vps运营商了，目前速度和服务都不错的有搬瓦工、Vultr、Linode和DigitalOcean等，很多很便宜的或用的人很少的就不用考虑了。

搬瓦工低价套餐现在基本买不到，最便宜的要$49.99一年，而且换IP要收费几美元，要是ip被ban就很亏。其他三家貌似都差不多，vultr优惠较多，所以我选择vultr。

## 购买VPS

vultr注册地址：https://www.vultr.com/?ref=7368419 ，可以使用paypal或支付宝付款。

vultr是折算成小时计费，服务一直部署着就一直计费。不用的话服务器需要destroy删除才停止计费，仅仅stop还是会计费。删除再新开服务器只需要$0.01。

每月有流量限制，超出额外收费。北美和西欧地区的服务器超出流量后，多出的部分收费为0.01美元/G。新加坡和日本东京（日本）为0.025美元/G，悉尼（澳大利亚）为0.05美元/G。把vultr服务器删掉，开通新的服务器，流量会从0开始重新计算。

vultr目前来说对国内最友好的机房就是日本东京机房和美国洛杉矶机房，其他机房不值得选购。

注意系统类型选择centos6，entos7默认的防火墙可能会干扰ssr的正常连接。

部署好之后ping服务器的ip，所以发现ping不通ip删除再部署，用新的ip重新测一下。一般延迟100多毫秒还行。

## 部署VPS

下载Xshell软件远程连接vps，连接成功后输入以下指令安装ssr：

```
yum -y install wget
wget -N --no-check-certificate https://softs.fun/Bash/ssr.sh && chmod +x ssr.sh && bash ssr.sh
```

设置好端口和密码，其他选项可以用默认设置。

## bbr加速

加速方式有bbr、锐速、kcptun等，这里选择谷歌的bbr。加速后速度提升明显，youtube 1080p无压力。

输入以下命令：

```
yum -y install wget
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh
chmod +x bbr.sh
./bbr.sh
```

完成操作后重启服务器，至此服务端配置完成。

## ssr客户端安装

ssr客户端地址：https://github.com/shadowsocksr-backup，各版本都有。

## 参考

- [自建ss服务器教程](https://github.com/Alvin9999/new-pac/wiki/%E8%87%AA%E5%BB%BAss%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%99%E7%A8%8B)
- [VPS 艹机狂魔](https://zhuanlan.zhihu.com/VPS-youhuima)

