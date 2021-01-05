## CentOS7 PXE 自动化安装

- 操作系统版本为`CentOS Linux release 7.9.2009 (Core)`图形界面
- 客户端操作系统内存大小 > 1024M
- 客户端root用户M默认密码为：root


**1.配置httpd镜像仓库**

```
~]# yum install dhcp tftp-server syslinux httpd
~]# mount /dev/cdrom  /var/www/html/centos/7/x86_64/
~]# cat /etc/yum.repos.d/cdrom.repo  < EOF
[development]
name=cdrom
baseurl=http://192.168.110.254/centos/7/x86_64/
gpgcheck=0
enable=1
EOF
~]# systemctl start httpd && systemctl enable httpd
```

**2. 配置 dhcp 服务**

```
~]# cp /usr/share/doc/dhcp*/dhcpd.conf.example /etc/dhcp/dhcpd.conf
~]# grep -v "^#" /etc/dhcp/dhcpd.conf |grep -v "^$"
option domain-name "linux.io";
option domain-name-servers 114.114.114.114, 8.8.8.8;
default-lease-time 43200;
max-lease-time 86400;
subnet 192.168.110.0 netmask 255.255.255.0 {
  range 192.168.110.100 192.168.110.200;
  option broadcast-address 192.168.110.255;
  option routers 192.168.110.1;
  option subnet-mask 255.255.255.0;
  next-server 192.168.110.254;
  filename "pxelinux.0";
}
~]# systemctl  start dhcpd && systemctl  enable dhcpd
```
**3. 制作 kickstart 文件

```
~]# yum install -y system-config-kickstart
~]# system-config-kickstart
```

**4. 配置tftp服务**

```
~]# cp /usr/share/syslinux/pxelinux.0 /var/lib/tftpboot/
~]# cp /var/www/html/centos/7/x86_64/images/pxeboot/{vmlinuz,initrd.img}  /var/lib/tftpboot/
~]# cp /usr/share/syslinux/{chain.c32,mboot.c32,menu.c32,memdisk} /var/lib/tftpboot/
~]# mkdir /var/lib/tftpboot/pxelinux.cfg/
~]# cat /var/lib/tftpboot/pxelinux.cfg/default 
default menu.c32
  prompt 5
  timeout 30
  MENU TITLE CentOS 7 PXE Menu

  LABEL linux
  MENU LABEL Install CentOS 7 x86_64 Mini
  KERNEL vmlinuz
  APPEND initrd=initrd.img inst.repo=http://192.168.110.254/centos/7/x86_64/ ks=http://192.168.110.254/centos/7/ks/centos7-mini.cfg

  LABEL linux
  MENU LABEL Install CentOS 7 x86_64 Desktop
  KERNEL vmlinuz
  APPEND initrd=initrd.img inst.repo=http://192.168.110.254/centos/7/x86_64/ ks=http://192.168.110.254/centos/7/ks/centos7-desk.cfg
~]# systemctl  start tftp.socket && systemctl enable tftp.socket
```
