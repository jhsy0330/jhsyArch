#### 检查网络连接是否正常

ip link

#### 给硬盘分区

fdisk

#### 格式化分区

mkfs.fat -F32 /dev/sda1

mkfs.ext4 /dev/sda2

#### 开启交换分区

mkswap /dev/sda3

swapon /dev/sda3

#### 编辑源

vim /etc/pacman.conf

#### 挂载目录

mount /dev/sda2 /mnt

mkdir /mnt/boot

mount /dev/sda1 /mnt/boot

这里一定要挂载esp分区，如果不挂在的话后面装引导会找不到内核

#### 安装linux内核

pacstrap /mnt base linux linux-firmware

#### 生成fstab

genfstab -U /mnt >> /mnt/etc/fstab

#### 设置编码

vim /mnt/etc/locale.gen 

#### 设置语言

vim /mnt/etc/locale.conf 

LANG=en_US.UTF-8

#### 设置hostname和hosts

/mnt/etc/hostname

/mnt/etc/hosts

```
127.0.0.1 	localhost
::1			localhost
127.0.0.1 	hostname.domain hostname
```

#### 切换bash进入安装好的arch

arch-chroot /mnt

#### 安装编码

locale-gen

#### 设置时区域并同步硬件时间

ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

hwclock --systohc

#### 设置密码

passwd

#### 安装配置引导程序

pacman -S grub efibootmgr intel-ucode os-prober

mkdir /boot/grub

grub-mkconfig > /boot/grub/grub.cfg

grub-install --target=x86_64-efi --efi-directory=/boot

#### 安装软件

pacman -S dhcpcd vim base-devel 

#### 重启进入arch

reboot

