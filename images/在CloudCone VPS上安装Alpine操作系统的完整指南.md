# 在CloudCone VPS上安装Alpine操作系统的完整指南

## 操作须知
- ⚠️ 此脚本仅适用于CloudCone VPS，其他服务商请勿使用
- ⚠️ 操作过程会清空服务器数据，请谨慎操作
- ⚠️ 完成时间约6-8分钟（安装5分钟+重启1-3分钟）

## 环境准备
### 系统要求
- CloudCone KVM架构VPS
- 建议至少1GB内存配置

👉 [【点击查看】2025年最新CloudCone优惠码及特价云服务器方案汇总](https://bit.ly/Cloudcone)

## 安装步骤
1. 通过SSH连接到您的CloudCone VPS
2. 执行以下命令下载并运行安装脚本：

bash
wget --no-check-certificate https://donghaiair.com.cn/cloudcone2alpine.sh && chmod +x cloudcone2alpine.sh && ./cloudcone2alpine.sh

## 脚本功能解析
bash
#!/bin/sh
## cloudcone专用版，ovz6、ovz7、kvm请使用对应版本

logfile="/tmp/kvm-alpine.log"
if [ "$1" = "--step-chroot" ]; then
  # 安装基础软件包
  apk add --no-cache alpine-base linux-virt syslinux grub grub-bios e2fsprogs eudev openssh rng-tools rng-tools-openrc
  
  # 配置系统服务
  rc-update add networking boot
  rc-update add crond default
  rc-update add udev sysinit
  rc-update add udev-trigger sysinit
  rc-update add sshd default
  
  # 安装GRUB引导程序
  disk=$(fdisk -l|grep Disk|awk '{print $2}'|awk -F: '{print $1}')
  part=$(fdisk -l|grep Linux|sed -n '1p'|awk '{print $1}')
  grub-install $disk
  grub-mkconfig -o /boot/grub/grub.cfg
fi

## 网络配置说明
脚本会自动检测并配置：
- 主网卡设备（默认eth0）
- 当前IP地址
- 子网掩码
- 默认网关

## 常见问题
1. **安装失败怎么办？**
   - 检查/oldroot$logfile日志文件
   - 确保VPS有足够的磁盘空间（建议至少10GB）

2. **如何验证安装成功？**
   - 系统重启后尝试SSH重新连接
   - 执行`cat /etc/alpine-release`查看版本信息

## 注意事项
- 建议在操作前备份重要数据
- 首次登录后立即修改root密码
- 如需其他软件包，可通过`apk add`命令安装