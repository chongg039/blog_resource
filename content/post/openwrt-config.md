---
title: "极贰刷Openwrt及配置记录"
date: 2020-12-22T15:28:15+08:00
Description: ""
Tags: []
Categories: []

---

极路由-极贰(HC5761)刷openwrt19.07，以及各种配置小记。

### 0. 刷u-boot

极路由申请开发者权限，刷入不死u-boot是必要的先行步骤，这里不再赘述。
有疑问的请自行google。

注意第一次进入u-boot时最好将之前的配置备份一下。

### 1. 选择并烧录对应的openwrt版本

进入openwrt官网的Download页面，在`Stable Release Builds`目录下找到自己合适的版本的固件。
这里的极贰对应的固件目录选择`/19.07.5/targets/ramips/mt7620/hc5761-squashfs-sysupgrade.bin`

![514g9Y](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/514g9Y.png#center)

然后将路由器断电断wan，lan口和电脑网口直连，按住reset后上电5s左右至灯快速闪烁，松开reset。

待电脑分配到ip后（如果不能需要手动指定一下192.168.1.x），进入192.168.1.1烧录刚才下载好的固件，
等待重启。

```
注：本文我采用了撰写本文时最新的19.07.05，目前没有发现问题。但在此两周前的最新版本19.07.04中
经常出现断流，我的这台极贰高发于凌晨零点刚过那一刻。

后来查阅内核日志google了一下，发现同样的问题被发现于19.07.04中：
https://bugs.openwrt.org/index.php?do=details&task_id=3344

该文章的解决方案中说明了降级到19.07.03便没有再出现过此类问题。我在动笔撰写该文章的前几天，openwrt官方
释出了19.07.05版本，便采用了该版本的固件且暂时没有重现上述问题。特此记录。

2020.12.27更新：19.07.05又出现了这个问题，日志显示发生在11:59:12，详细信息记录：

[135769.808729] ------------[ cut here  ]------------
[135769.813559] WARNING: CPU: 0 PID: 0 at net/sched/sch_generic.c:320 0x802c4760
[135769.820863] NETDEV WATCHDOG: eth0 (mtk_soc_eth): transmit queue 0 timed out
[135769.828050] Modules linked in: rt2800soc rt2800mmio rt2800lib pppoe ppp_async mt76x0e mt76x0_common rt2x00soc rt2x00mmio rt2x00lib pppox ppp_generic nf_conntrack_ipv6 mt76x02_lib mt76 mac80211 iptable_nat ipt_REJECT ipt_MASQUERADE cfg80211 xt_time xt_tcpudp xt_state xt_socket xt_nat xt_multiport xt_mark xt_mac xt_limit xt_conntrack xt_comment xt_TPROXY xt_TCPMSS xt_REDIRECT xt_LOG xt_FLOWOFFLOAD xt_CT slhc nf_socket_ipv6 nf_socket_ipv4 nf_reject_ipv4 nf_nat_redirect nf_nat_masquerade_ipv4 nf_conntrack_ipv4 nf_nat_ipv4 nf_nat nf_log_ipv4 nf_flow_table_hw nf_flow_table nf_defrag_ipv6 nf_defrag_ipv4 nf_conntrack_rtcache nf_conntrack_netlink nf_conntrack iptable_mangle iptable_filter ip_tables crc_ccitt compat ledtrig_usbport xt_set ip_set_list_set ip_set_hash_netportnet ip_set_hash_netport ip_set_hash_netnet
[135769.901131]  ip_set_hash_netiface ip_set_hash_net ip_set_hash_mac ip_set_hash_ipportnet ip_set_hash_ipportip ip_set_hash_ipport ip_set_hash_ipmark ip_set_hash_ip ip_set_bitmap_port ip_set_bitmap_ipmac ip_set_bitmap_ip ip_set nfnetlink nf_log_ipv6 nf_log_common ip6table_mangle ip6table_filter ip6_tables ip6t_REJECT x_tables nf_reject_ipv6 usb_storage sd_mod scsi_mod ext4 mbcache jbd2 crc16 crc32c_generic crypto_hash mmc_block mtk_sd mmc_core leds_gpio ohci_platform ohci_hcd ehci_platform ehci_hcd gpio_button_hotplug usbcore nls_base usb_common
[135769.950400] CPU: 0 PID: 0 Comm: swapper Not tainted 4.14.209 #0
[135769.956533] Stack : 8046fee0 80054428 80480000 8042e860 00000000 00000000 00000000 00000000
[135769.965181]         00000000 00000000 00000000 00000000 00000000 00000001 87c07de0 ac07f5c8
[135769.973823]         87c07e78 00000000 00000000 00005608 00000038 803cdd18 00000008 00000000
[135769.982469]         00000000 80460000 000e8080 70617773 87c07dc0 00000000 00000000 80428714
[135769.991116]         802c4760 00000140 8046ff34 8046fef4 00000003 80214c40 00000000 805b0000
[135769.999762]         ...
[135770.002349] Call Trace:
[135770.002361] [<80054428>] 0x80054428
[135770.008629] [<803cdd18>] 0x803cdd18
[135770.012309] [<802c4760>] 0x802c4760
[135770.015957] [<80214c40>] 0x80214c40
[135770.019633] [<8000aa50>] 0x8000aa50
[135770.023280] [<8000aa58>] 0x8000aa58
[135770.026934] [<80026530>] 0x80026530
[135770.030599] [<802c4760>] 0x802c4760
[135770.034262] [<800265b8>] 0x800265b8
[135770.037905] [<8026ce60>] 0x8026ce60
[135770.041597] [<802c4760>] 0x802c4760
[135770.045255] [<802c45fc>] 0x802c45fc
[135770.048919] [<8006009c>] 0x8006009c
[135770.052565] [<800012fc>] 0x800012fc
[135770.056226] [<87574a80>] 0x87574a80 [mt76x02_lib@87570000+0x95e0]
[135770.062565] [<80060270>] 0x80060270
[135770.066232] [<80049ae4>] 0x80049ae4
[135770.069920] [<803d37c8>] 0x803d37c8
[135770.073566] [<8005a624>] 0x8005a624
[135770.077211] [<80055b80>] 0x80055b80
[135770.080890] [<80005e90>] 0x80005e90
[135770.084544] 
[135770.086153] ---[ end trace c5a92886e79b758e  ]---
```

### 2. 换肤

openwrt默认的界面比较丑，容易影响到接下来的工作心情，所以上来先换个皮。

我们先更换一下opkg的源为清华的镜像源：[链接](https://mirrors.tuna.tsinghua.edu.cn/help/openwrt/)

然后执行一下`opkg update`，当重启或者找不到包时都需要执行一遍该命令。

皮肤我采用了luci-theme-argon：[链接](https://github.com/jerrykuku/luci-theme-argon)

安装前为了访问https的链路，需要
```shell
opkg install libustream-openssl
或
opkg install ca-certificates
```

为了支持中文界面，再安装一个中文语言包：
```shell
opkg install luci-i18n-base-zh-cn```

![a1SIyy](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/a1SIyy.png#center)

### 3. extroot扩容

板载的存储空间一般比较小，能够安装ipk的空间一般也就剩余几M到十几M，但是大多数都支持sd卡或插入U盘来扩容。

为了为下一步的安装v2ray-core这种比较大的文件做准备，这里我插入了一张8G的SD卡来扩展根分区的空间。

openwrt支持`pivot-overlay`或者`pivot-root`两种方式扩充，具体原理不是很明白，但大意是第一种扩充`overlay`分区，
opkg会将软件包安装在这个目录下，第二种则是干脆利落扩充根分区。这两种方式都尝试了一下，但是采用`pivot-overlay`
的方式扩充之后又一次导致无法开机只能重新烧rom，所以在这里还是比较推荐扩充根分区的第二种方式。

具体的步骤推荐看这篇文章：https://www.solarck.com/openwrt-extroot.html

这里也再贴一下做个记录：

1. 安装必要的包：
```shell
opkg update
opkg install e2fsprogs kmod-usb-core kmod-usb2 kmod-usb-storage usbutils kmod-fs-ext4 block-mount
```
如果是SD卡需要额外
```shell
opkg install fdisk
```
2. 查看是否正确识别设备：
```shell
lsusb 或 fdisk -l
```
3. 格式化设备：
```shell
mkfs.ext4 /dev/你的设备名
```
4. 挂载到当前系统：
```shell
mount /dev/你的设备名 /mnt
```
5. 创建一个128M的swap文件：
```shell
dd if=/dev/zero of=/mnt/swap bs=2048 count=65536
mkswap /mnt/swap
swapon /mnt/swap
```
6. 把`\`目录下的文件迁移到U盘，pivot-root方式，适用于Barrier Breaker（trunk）版本：
```shell
mkdir -p /tmp/cproot
mount --bind / /tmp/cproot
tar -C /tmp/cproot -cvf - . | tar -C /mnt/ -xf -
umount /tmp/cproot
```
7. 编辑`/etc/config/fstab`文件，若没有则要生成一个：
```shell
config 'mount'
        option target        '/'
        option device        '/dev/你的设备名'
        option fstype        'ext4'
        option options       'rw,sync'
        option enabled       '1'
        option enabled_fsck  '0'
```
8. 重启查看是否生效：
![nHaul5](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/nHaul5.png#center)

现在安装软件的空间也已经足够大了。

![rdx0yl](https://cdn.jsdelivr.net/gh/chongg039/blog-pic-repo@master/uPic/rdx0yl.png#center)

### 4. 配置v2ray

这个主要是安装两个，v2ray-core和界面用的luci-app-v2ray。

v2ray-core链接：https://github.com/kuoruan/openwrt-v2ray

luci-app-v2ray链接：https://github.com/kuoruan/luci-app-v2ray

基本的安装流程文档中都有。之前说过v2ray-core的ipk比较大，我们使用了SD卡扩充了空间。另一个问题是
较大的安装包opkg可能会下载失败，这里我是先下载下来，再上传手动安装的方式。

由于我使用的v2ray的协议是比较新的VLESS协议，v2ray-core最新版已经支持，但luci-app-v2ray还没有。
为了在界面中能够配置VLESS协议的链接，需要按照这个issue进行修改：https://github.com/kuoruan/luci-app-v2ray/issues/294

最后配置一下转发和防火墙就可以了。

### 5. 路由器中继配置

openwrt wan口接主路由lan口出来的线，需要修改openwrt的ip为不同网段。

比如主路由为192.168.1.1，则openwrt可设为192.168.2.x，这里我设为了192.168.2.1。
然后主路由下可以绑定openwrt的静态ip地址。

接入openwrt的节点设为dhcp即可。
