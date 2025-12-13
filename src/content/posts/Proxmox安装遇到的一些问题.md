---
title: Proxmox安装遇到的一些问题
published: 2025-12-12
description: ''
image: ''
tags: 
  - 安装
  - Proxmox
category: ''
draft: false 
lang: ''
---
在 Proxmox 安装启动菜单中，
选择 “Install Proxmox”（安装 Proxmox），然后按 “e “键编辑命令。
找到 “squiet splash = silent “一行，将其替换为 “nomodeset”。
按 F10 继续安装。安装过程中应该不会遇到黑屏问题。

https://dev-pages.info/troubleshooting-proxmox-installation-issues-fixing-black-screen-problem/


出现错误,按Ctrl+ALT+F2查看发现错误
Cannot run in framebuffer mode. please specify busIDs for all framebuffer devices

按Ctrl+Alt+F1返回控制台
执行命令查看显卡pci位置:lspci| grep -i vga

我的是0c:00.0
在 /usr/share/X11/xorg.conf.d/ 中创建一个驱动程序描述文件，例如nvdia.conf
Section "Device"
    Identifier "Card0"
    Driver "fbdev"
    BusID "pci0:0c:0:0:"
EndSection
然后执行命令启动gui

xinit -- -dpi 96 >/dev/tty2 2>&1

完美进入安装程序.

https://forum.proxmox.com/threads/generic-solution-when-install-gets-framebuffer-mode-fails.111577/
https://eatm.app/archives/3566.html

禁用pve-daily-update.service

查找存在的计划服务：systemctl list-timers –all。经过查询，得到以下系统定时运行服务：
systemd-tmpfiles-clean.service
logrotate.service
man-db.service
apt-daily.service
pve-daily-update.service
apt-daily-upgrade.service
e2scrub_all.service
fstrim.service

禁用定时器才能禁用服务
systemctl disable pve-daily-update.timer
systemctl disable apt-daily-upgrade.timer
systemctl disable apt-daily.timer

systemctl list-timers –all

https://www.ippa.top/1066.html