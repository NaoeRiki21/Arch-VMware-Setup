# 安装Arch-Linux
记录一下第一次在VMwear里安装archlinux
（不太熟练markdown语法）

### 开始
>校准时间
> >`timedatectl ste-ntp true`
>
>分区
>>`cfdisk /dev/nvme0n1`
>>> 新建Linux Filesystem (任意大小)、BIOS boot (2MB)、Linux Swap (4GB+-)格式的分区
>>> whrite 输入 yes 退出
>
>格式化分区
>>`mkfs.ext4 /dev/nvme0n1p1`
>>`mkswap /dev/nvme0n1p2`
>>`swapon /dev/nvme0n1p2`
>
>挂载分区
>>`mount /dev/nvme0n1p1 /mnt`
>
>安装基本系统
>>`pacstrap /mnt base linux linux-firmware`
>
>生成 fstab 文件
>>`genfstab -U /mnt >> /mnt/etc/fstab`
>
>进入新系统环境
>>`arch-chroot /mnt`
>
>完成

>开始下一阶段 Arch初始化
>
>安装必要程序
>>`pacman -S vim nano sudo openssh`
>
>更改时区
>>`timedatectl ste-timezon Asia/Shanghai`
>
>添加普通用户
>>`useradd -m -G wheel -s /bin/bash <你的用户名>`
>>>`passwd <你的用户名>`
>
>配置网络
>>`ip a`
>>>`vim /etc/systemd/network/20-wired.network`
```
[Match]
Name=ens33  # *** 这里的 ens33 就是你的网卡名称 ***

[Network]
DHCP=yes   # 使用DHCP自动获取IP地址
```
>> 保存并退出
>启动服务
```
systemctl enable systemd-networkd
systemctl start systemd-networkd
systemctl enable systemd-resolved
systemctl start systemd-resolved
```
>测试连接
>>`ping archlinux.org`
>
> 安装并配置引导加载程序
>> 安装grub
```
pacman -S grub efibootmgr  # 如果是UEFI启动
pacman -S grub             # 如果是BIOS/Legacy启动
```
> `grub-install /dev/nvme0n1`
> `grub-mkconfig -o /boot/grub/grub.cfg`
>
>退出 chroot 环境
>>`exit`
>卸载已挂载的分区
>>`umount -R /mnt`
>拔出u盘
>>`reboot`
>
### 完成安装


可选操作
>安装常用程序
>>`pacman -S git networkmanager bash-completion htop unzip zip tree wget curl`
>
>配置sudo
>>`vim /etc/sudoers`
>>将 `# %wheel ALL=(ALL:ALL) ALL` 前的 “#” 删除
>
>配置ssh服务开机自启动
```
systemctl enable sshd.service
systemctl start sshd.service
```
>
