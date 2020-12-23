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

### 5. 中继配置
