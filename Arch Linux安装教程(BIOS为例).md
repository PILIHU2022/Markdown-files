---
title: Arch Linux安装教程
date: 2023-08-15 13:18:20
tags: Arch Linux
---
# 如何安装Arch Linux,是一个困扰小白很久的问题,没问题,今天安装教程来了
## 有一定的基础可以查阅[Arch Wiki的安装指南](https://wiki.archlinuxcn.org/wiki/%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97)

# 下载Arch Linux LiveCD
作者在广东，ping过发现USTC镜像站延时最低，于是使用USTC源，也可以使用BT下载
[BT链接](https://archlinux.org/releng/releases/2023.08.01/torrent/)
也可以:
[磁力链接](magnet:?xt=urn:btih:7a9c4a72e79fcf5f65f091e462b60e589af3f865&dn=archlinux-2023.08.01-x86_64.iso)
还可以:
[USTC镜像站下载](https://mirrors.ustc.edu.cn/archlinux/iso/2023.08.01/archlinux-2023.08.01-x86_64.iso)

# 启动到LiveCD环境
如果嫌使用命令行安装麻烦,可以尝试官方的一个工具`archinstall`运行命令如下:
```
archinstall
```
但是不建议小白使用,因为这样学不到什么东西

# 验证引导方式
```
ls /sys/firmware/efi/efivars
```
若有输出则是UEFI引导，若无则是BIOS或CSM引导

# 连接网络
- 确保系统已启用了网络接口
```
ip link
```
- 直接使用有线连接
- 使用无线网卡联网，可参阅[Arch Wiki-iwctl](https://wiki.archlinuxcn.org/wiki/Iwd#iwctl)
   - **连接WiFi输入密码时不会显示类似于"*"等，也无法查看到你输入的密码，请认真输入**

# 创建硬盘分区
首先需要知道设备名称，如sda，nvme0n1等
```
lsblk -f # 查看设备名，双系统的话会显示硬盘格式，如NTFS等等
```
确定好了设备名称，开始分区
```
fdisk /dev/sda # 这里的sda为刚刚所查询到的设备名称
```
这里是Arch Wiki的分区方案示例：

BIOS与MBR分区表方案：

| 挂载点 |   分区   |     分区类型      | 建议大小  |
| :---: | :-----: | :-------------: | :-----: |
| [SWAP] | /dev/swap | Linux swap(交换空间) | 大于512MB |
|  /mnt  | /dev/root |      Linux       | 剩余空间  |

## 创建分区
```
Command (m for help): g # 新建GPT分区表，输入o新建MBR分区表
Created a new GPT disklabel (GUID: B10348B1-3BF7-4479-9262-B200606F1D8A).

Command (m for help): n # 新建一个分区
Partition number (1-128, default 1): # 此处回车
First sector (2048-41943006, default 2048): # 此处回车
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-41943006, default 41940991): +xG # 为新建的分区设置容量，"x"为你想设置的容量，"+"不可省略
Created a new partition 1 of type 'Linux filesystem' and of size 10 GiB.

Command (m for help): n
Partition number (2-128, default 2): # 此处回车
First sector (20973568-41943006, default 20973568): # 此处回车
Last sector, +/-sectors or +/-size{K,M,G,T,P} (20973568-41943006, default 41940991): # 此处回车
Created a new partition 2 of type 'Linux filesystem' and of size 10 GiB.

Command (m for help): t # 更改分区类型
Partition number (1,2, default 2): 1 # 此处的1为系统盘，是容量较大的
Partition type or alias (type L to list all): 20 # 设置为
Changed type of partition 'Linux filesystem' to 'Linux filesystem'.

Command (m for help): t
Partition number (1,2, default 2): 2 # 此处的2为交换分区，是容量较小的
Partition type or alias (type L to list all): 19
Changed type of partition 'Linux filesystem' to 'Linux swap'.

Command (m for help): w # 写入并保存
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

## 格式化分区
将系统分区格式化
```
mkfs.btrfs -L system /dev/sdxy # 此处的sdx为设备名，y为数字，如sda1等。将系统分区格式化为btrfs格式
```
将SWAP分区格式化
```
mkswap -L SWAP /dev/sdxy # 同上，并且将SWAP分区格式化
```
启用SWAP分区
```
swapon /dev/sdxy
```