---
layout: '../../layouts/MarkdownPost.astro'
title: 'Centos9 Stream 开局配置'
pubDate: 2023-03-06
description: '体验 CentOs9以及进行基础配置'
author: 'zhourj'
cover:
    url: 'https://s2.loli.net/2023/03/06/wEurLNpQ52Ml71v.png'
    square: 'https://s2.loli.net/2023/03/06/wEurLNpQ52Ml71v.png'
    alt: 'cover'
tags: ["源码研究", "Centos", "linux"] 
theme: 'light'
featured: true
---
## 1、 使用国内镜像源
```xml
cat >  /etc/yum.repos.d/centos.repo << EOF 

[AppStream]
name=CentOS-\$releasever - AppStream - mirrors.ustc.edu.cn
#failovermethod=priority
baseurl=https://mirrors.ustc.edu.cn/centos-stream/\$stream/AppStream/\$basearch/os/
gpgcheck=1
gpgkey=https://mirrors.ustc.edu.cn/centos-stream/RPM-GPG-KEY-CentOS-Official

[BaseOS]
name=CentOS-\$releasever - BaseOS - mirrors.ustc.edu.cn
#failovermethod=priority
baseurl=https://mirrors.ustc.edu.cn/centos-stream/\$stream/BaseOS/\$basearch/os/
gpgcheck=1
gpgkey=https://mirrors.ustc.edu.cn/centos-stream/RPM-GPG-KEY-CentOS-Official

[CRB]
name=CentOS-\$releasever - CRB - mirrors.ustc.edu.cn
#failovermethod=priority
baseurl=https://mirrors.ustc.edu.cn/centos-stream/\$stream/CRB/\$basearch/os/
gpgcheck=1
gpgkey=https://mirrors.ustc.edu.cn/centos-stream/RPM-GPG-KEY-CentOS-Official

[HighAvailability]
name=CentOS-\$releasever - HighAvailability - mirrors.ustc.edu.cn
#failovermethod=priority
baseurl=https://mirrors.ustc.edu.cn/centos-stream/\$stream/HighAvailability/\$basearch/os/
gpgcheck=1
gpgkey=https://mirrors.ustc.edu.cn/centos-stream/RPM-GPG-KEY-CentOS-Official

[NFV]
name=CentOS-\$releasever - NFV - mirrors.ustc.edu.cn
#failovermethod=priority
baseurl=https://mirrors.ustc.edu.cn/centos-stream/\$stream/NFV/\$basearch/os/
gpgcheck=1
gpgkey=https://mirrors.ustc.edu.cn/centos-stream/RPM-GPG-KEY-CentOS-Official

[RT]
name=CentOS-\$releasever - RT - mirrors.ustc.edu.cn
#failovermethod=priority
baseurl=https://mirrors.ustc.edu.cn/centos-stream/\$stream/RT/\$basearch/os/
gpgcheck=1
gpgkey=https://mirrors.ustc.edu.cn/centos-stream/RPM-GPG-KEY-CentOS-Official

[ResilientStorage]
name=CentOS-\$releasever - ResilientStorage - mirrors.ustc.edu.cn
#failovermethod=priority
baseurl=https://mirrors.ustc.edu.cn/centos-stream/\$stream/ResilientStorage/\$basearch/os/
gpgcheck=1
gpgkey=https://mirrors.ustc.edu.cn/centos-stream/RPM-GPG-KEY-CentOS-Official
EOF
```
## 2、更新源信息
```xml
yum makecache && yum update
```
## 3、配置网卡
```txt
# 查看网卡配置
cat /etc/NetworkManager/system-connections/ens160.nmconnection
[[connection]
id=enp0s5
uuid=4bef2a4e-a532-330a-8b3c-61e2ebce0883
type=ethernet
autoconnect-priority=-999
interface-name=enp0s5
timestamp=1672036689
onboot=yes
[ethernet]

[ipv4]
address1=10.211.55.101/24,10.211.55.1
dns=8.8.8.8
method=manual
[ipv6]
addr-gen-mode=eui64
method=auto

[proxy]

nmcli c reload                         # 重新加载配置文件
nmcli c up ens160                      # 重启ens160网卡
```
## 4、测试网络
```xml
# 测试网络
[root@chenby ~]# ping www.oiox.cn -4
PING  (117.161.38.205) 56(84) bytes of data.
64 bytes from 117.161.38.205 (117.161.38.205): icmp_seq=1 ttl=55 time=6.84 ms
64 bytes from 117.161.38.205 (117.161.38.205): icmp_seq=2 ttl=55 time=6.44 ms
64 bytes from 117.161.38.205 (117.161.38.205): icmp_seq=3 ttl=55 time=7.12 ms

---  ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 6.441/6.800/7.117/0.277 ms
[root@bogon ~]# ping www.oiox.cn -6
PING www.oiox.cn(js-ipv6 (2409:8c10:c00:1404:3b::)) 56 data bytes
64 bytes from js-ipv6 (2409:8c10:c00:1404:3b::): icmp_seq=1 ttl=56 time=5.94 ms
64 bytes from js-ipv6 (2409:8c10:c00:1404:3b::): icmp_seq=2 ttl=56 time=6.11 ms
64 bytes from js-ipv6 (2409:8c10:c00:1404:3b::): icmp_seq=3 ttl=56 time=6.01 ms

--- www.oiox.cn ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2002ms
rtt min/avg/max/mdev = 5.941/6.020/6.107/0.067 ms
```
## 5、设置主机名和映射关系
```xml
查看当前主机名 hostnamectl

sudo nmcli g hostname
sudo nmcli g hostname host.myfreax.com
sudo systemctl restart systemd-hostnamed
或者
vim /etc/hostname
node01
vi /etc/hosts
192.168.0.120 node01
rebbot
```
## 6、关闭防火墙
```xml
查看防火墙状态
systemctl status firewalld
firewall-cmd --state

开启防火墙
systemctl start firewalld
关闭防火墙
systemctl stop firewalld
重启防火墙
systemctl restart firewalld
```
## 7、关闭seLINUX
```xml
vim /etc/selinux/config 
selinux  disabled
or
确认grubby软件包是否已经安装

# rpm -q grubby

要永久禁用 SELinux：

配置您的引导加载程序以添加selinux=0到内核​​命令行：

# grubby --update-kernel ALL --args selinux=0

重新启动系统：

# reboot

确认

重启后，getenforce命令确认返回结果为Disabled
```
## 8、时间同步
```xml
dnf -y install chrony

vim /etc/chrony.conf
在文件底部新添加一行 pool dlp.srv.world iburst

设置开机自启
systemctl enable --now chronyd

查看状态
chronyc sources
#启用时间同步
timedatectl set-ntp true
#禁用时间同步
timedatectl set-ntp false
同步硬件状态
hwclock -w
```
## 9、安装 JDK
```xml
事先查看是否自带jdk
通过命令查看系统已安装的jdk
rpm -qa | grep java  或 rpm -qa | grep jdk 命令来查询出系统自带的jdk（蓝框的四个就是系统自带的）注：其余的不要删
rpm -e --nodeps   后面跟系统自带的jdk名    这个命令来删除系统自带的jdk，
安装jdk
rpm -i   jdk-8u181-linux-x64.rpm	
		*有一些软件只认：/usr/java/default
	vi /etc/profile     
		export  JAVA_HOME=/usr/java/default
		export PATH=$PATH:$JAVA_HOME/bin
	source /etc/profile   |  .    /etc/profile
```
## 10、配置 ssh 免密（还需查询）
```xml
ssh  localhost  1,验证自己还没免密  2,被动生成了  /root/.ssh
		ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
		cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
```

